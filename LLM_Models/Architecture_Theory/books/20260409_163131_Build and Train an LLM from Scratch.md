# 번역 기사

- **원본 URL:** https://ai.gopubby.com/build-and-train-an-llm-from-scratch-e11f0e73dbcc
- **번역 일시:** 2026-04-09 16:31:31

---

# Build and Train an LLM from Scratch

이전 강좌 'Into AI'에서는 완전한 디코더 전용 트랜스포머(Decoder-only Transformer)를 처음부터 구현하는 방법을 배웠습니다. 다음으로 모델을 위한 토크나이저(tokenizer)를 구축하는 방법을 익혔습니다.

이제 다음 단계로 나아가, 구성 요소를 다시 살펴보고 텍스트 생성 모델을 엔드투엔드(end-to-end) 방식으로 훈련할 때입니다.

시작해 봅시다!

## 데이터 준비하기

### 1. 데이터셋 다운로드

모델 훈련을 위해 Hugging Face의 WikiText 데이터셋을 사용합니다. 이 데이터셋은 검증된 위키백과(Wikipedia) 문서에서 파생되었으며 약 1억 3백만 단어를 포함합니다.

다운로드 방법은 다음과 같습니다.

```python
from datasets import load_dataset

# Load WikiText dataset ("train" subset)
dataset = load_dataset("wikitext", "wikitext-103-v1", split="train")
```

이 데이터셋의 훈련 예시를 확인해 봅시다.

```python
# Training example
print(dataset['text'][808])
```

```
Output:
Organisations in the United Kingdom ( UK ) describe GA in less restrictive terms that include elements of commercial aviation . The British Business and General Aviation Association interprets it to be " all aeroplane and helicopter flying except that performed by the major airlines and the Armed Services " . The General Aviation Awareness Council applies the description " all Civil Aviation operations other than scheduled air services and non @-@ scheduled air transport operations for remuneration or hire " . For the purposes of a strategic review of GA in the UK , the Civil Aviation Authority ( CAA ) defined the scope of GA as " a civil aircraft operation other than a commercial air transport flight operating to a schedule " , and considered it necessary to depart from the ICAO definition and include aerial work and minor CAT operations . 
```

훈련 데이터셋에서 비어 있지 않은 모든 텍스트 라인을 연결하고, 각각을 두 개의 개행 문자(`\n\n`)로 구분합니다.

```python
training_text = "\n\n".join([text.strip() for text in dataset['text'] if text.strip()])
```

`training_text`의 짧은 하위 집합이 아래에 나와 있습니다.

```python
# Examine a subset of 'training_text'
training_text[12003:12400]
```

```
Output:
was initially recorded using orchestra , then Sakimoto removed elements such as the guitar and bass , then adjusted the theme using a synthesizer before redoing segments such as the guitar piece on their own before incorporating them into the theme . The rejected main theme was used as a hopeful tune that played during the game 's ending . The battle themes were designed around the concept of a
```

`training_text`의 길이는 아래와 같습니다.

```python
print(f"Training text length: {len(training_text):,} characters")
```

```
Output: Training text length: 535,923,053 characters
```

### 2. 데이터셋 토큰화

지난 강좌에서 생성한 토크나이저(Tokenizer)를 사용하여 데이터셋을 토큰화할 차례입니다.

```python
# Character level tokenizer
class Tokenizer:
  def __init__(self, text):
    # Special token for characters not in vocabulary
    self.UNK = "<UNK>"

    # All characters in vocabulary
    self.chars = sorted(list(set(text)))
    self.chars += [self.UNK]

    # Vocabulary size 
    self.vocab_size = len(self.chars)

    # Mapping from character to ID
    self.char_to_id = {char: id for id, char in enumerate(self.chars)}

    # Mapping from ID to character
    self.id_to_char = {id: char for id, char in enumerate(self.chars)} 
  
  # Convert text string to list of token IDs
  def encode(self, text):
    return [self.char_to_id.get(ch, self.char_to_id[self.UNK]) for ch in text]
  
  # Convert list of token IDs to text string
  def decode(self, ids):
    return "".join(self.id_to_char.get(id, self.UNK) for id in ids)
```

다음과 같이 진행합니다.

```python
# Create an instance of tokenizer
tokenizer = Tokenizer(training_text)

# Vocabulary size 
vocab_size = tokenizer.vocab_size
print(f"Vocabulary size: {vocab_size}")
```

```
Output: Vocabulary size: 1251
```

어휘 크기(vocabulary size)가 1251자임을 주목하십시오. 영어에는 26개의 알파벳만 있는데, 왜 어휘 크기가 이렇게 클까요?

어휘(vocabulary)를 살펴보면서 포함된 일부 문자를 확인해 봅시다.

```python
print(" ".join(tokenizer.chars[800:850]))
```

```
Output:
⅓ ⅔ ⅛ ⅜ ⅝ ⅞ ← ↑ → ↓ ↔ ↗ ↦ ↪ ⇄ ⇌ ∀ ∂ ∆ ∈ ∑ − ∕ ∖ ∗ ∘ √ ∝ ∞ ∩ ∪ ∴ ∼ ≈ ≠ ≡ ≢ ≤ ≥ ≪ ⊂ ⊕ ⊗ ⊙ ⊥ ⋅ ⋯ ⌊ ⌋ ①
```

다시 강좌로 돌아옵시다!

### 3. 언어 모델링에 필요한 데이터셋 생성

데이터셋을 토큰화한 후에는 훈련 중 데이터를 로드하고 제공해야 합니다. 여기서 PyTorch의 `Dataset` 클래스를 상속받는 `TextDataset` 클래스가 등장하며, 다음 메서드들을 가집니다.

*   `__init__`: 주어진 텍스트를 토큰화하고, 각 시퀀스의 최대 길이가 `max_seq_length`인 고정 길이 훈련 시퀀스를 얼마나 많이 생성할 수 있는지 계산합니다.
*   `__len__`: 사용 가능한 훈련 시퀀스의 수를 반환합니다.
*   `__getitem__`: 주어진 인덱스에 해당하는 훈련 시퀀스와 그 타겟(target, 한 위치 앞으로 이동된 토큰)을 반환합니다.

```python
from torch.utils.data import Dataset, DataLoader

# Dataset for language modeling
class TextDataset(Dataset):
  def __init__(self, text, tokenizer, max_seq_length):
    # Convert text to token IDs
    self.tokens = tokenizer.encode(text)

    # Maximum length of each training sequence
    self.max_seq_length = max_seq_length

    # Number of valid training sequences
    self.num_sequences = (len(self.tokens) - 1) // max_seq_length

  # Total number of valid training sequences
  def __len__(self):
    return self.num_sequences
  
  # Get an input sequence and targets
  def __getitem__(self, idx):
    # Start index of the sequence
    start = idx * self.max_seq_length

    # End index of the sequence
    end = start + self.max_seq_length

    # Input token sequence
    input_ids = torch.tensor(self.tokens[start:end], dtype=torch.long)

    # Next-token targets/ labels (shifted by one character)
    target_ids = torch.tensor(self.tokens[start+1:end+1], dtype=torch.long)

    return input_ids, target_ids
```

`TextDataset` 클래스를 사용하여 훈련 데이터셋을 생성해 봅시다.

```python
# Define maximum training sequence length
max_seq_length = 128

# Create dataset
train_dataset = TextDataset(training_text, tokenizer, max_seq_length)
```

데이터셋의 훈련 시퀀스 수는 다음과 같습니다.

```python
print(f"Number of training sequences: {len(train_dataset):,}")
```

```
Output: Number of training sequences: 4,186,898
```

훈련 시퀀스와 그 타겟(target)은 다음과 같습니다.

```python
input, target = train_dataset[10]

print("Input IDs:\n", input)
print("\nTarget IDs:\n", target)

print("\nDecoded Input:\n", tokenizer.decode(input.tolist()))
print("\nDecoded Target:\n", tokenizer.decode(target.tolist()))
```

```
Output:
Input IDs:
 tensor([66, 81, 66, 79,  1, 13,  1, 66, 79, 69,  1, 88, 66, 84,  1, 81, 83, 66, 74, 84, 70, 69,  1, 67, 90,  1, 67, 80, 85, 73,  1, 43, 66, 81, 66, 79, 70, 84, 70,  1, 66, 79, 69,  1, 88, 70, 84, 85, 70, 83, 79,  1, 68, 83, 74, 85, 74, 68, 84,  1, 15,  1, 34, 71, 85, 70, 83,  1, 83, 70, 77, 70, 66, 84, 70,  1, 13,  1, 74, 85,  1, 83, 70, 68, 70, 74, 87, 70, 69,  1, 69, 80, 88, 79, 77, 80, 66, 69, 66, 67, 77, 70,  1, 68, 80, 79, 85, 70, 79, 85,  1, 13,  1, 66, 77, 80, 79, 72,  1, 88, 74, 85, 73,  1, 66, 79, 1, 70])
Target IDs:
 tensor([81, 66, 79,  1, 13,  1, 66, 79, 69,  1, 88, 66, 84,  1, 81, 83, 66, 74, 84, 70, 69,  1, 67, 90,  1, 67, 80, 85, 73,  1, 43, 66, 81, 66, 79, 70, 84, 70,  1, 66, 79, 69,  1, 88, 70, 84, 85, 70, 83, 79,  1, 68, 83, 74, 85, 74, 68, 84,  1, 15,  1, 34, 71, 85, 70, 83,  1, 83, 70, 77, 70, 66, 84, 70,  1, 13,  1, 74, 85,  1, 83, 70, 68, 70, 74, 87, 70, 69,  1, 69, 80, 88, 79, 77, 80, 66, 69, 66, 67, 77, 70,  1, 68, 80, 79, 85, 70, 79, 85,  1, 13,  1, 66, 77, 80, 79, 72,  1, 88, 74, 85, 73,  1, 66, 79, 1, 70, 89])
Decoded Input:
 apan , and was praised by both Japanese and western critics . After release , it received downloadable content , along with an e
Decoded Target:
 pan , and was praised by both Japanese and western critics . After release , it received downloadable content , along with an ex
```

입력 시퀀스와 타겟 시퀀스가 한 문자만큼 시프트(shifted)되어 있음에 주목하십시오. 이는 모델이 이전 문자를 기반으로 다음 문자를 예측하는 것을 학습하는 데 도움이 됩니다.

## 배치 훈련을 위한 DataLoader 설정

이 단계에서는 PyTorch `DataLoader`를 사용하여 `train_dataset`에서 `batch_size` 크기의 샘플들로 구성된 섞인 배치(shuffled batches)를 훈련용으로 로드하는 이터러블(iterable)을 생성합니다.

```python
# Define batch size
batch_size = 16

# Create training DataLoader
train_loader = DataLoader(train_dataset, batch_size=batch_size, shuffle=True, num_workers=0)
```

이는 다음과 같은 수의 훈련 데이터 배치(batches)를 생성합니다.

```python
print(f"Number of training sequences: {len(train_dataset):,}")

print(f"Number of batches per epoch: {len(train_loader):,}") # Number of training sequences / batch_size
```

```
Output:
Number of training sequences: 4,186,898
Number of batches per epoch: 261,682 
```

## 디코더 전용 트랜스포머 모델 설정

이 단계에서는 이전 강좌에서 구축한 모델을 훈련을 위해 설정합니다.

```python
import torch
import torch.nn as nn
import math

# Causal Multi-head Self-attention block
class CausalMultiHeadSelfAttention(nn.Module):
  def __init__(self, embedding_dim, num_heads):
    super().__init__()

    # Check if embedding_dim is divisible by num_heads
    assert embedding_dim % num_heads == 0, "embedding_dim must be divisible by num_heads"
    
    # Embedding dimension
    self.embedding_dim = embedding_dim

    # Number of total heads
    self.num_heads = num_heads

    # Dimension of each head
    self.head_dim = embedding_dim // num_heads

    # Linear projections for Q, K, V (to be split later for each head)
    self.W_q = nn.Linear(embedding_dim, embedding_dim, bias = False)
    self.W_k = nn.Linear(embedding_dim, embedding_dim, bias = False)
    self.W_v = nn.Linear(embedding_dim, embedding_dim, bias = False)

    # Linear projection to produce final output
    self.W_o = nn.Linear(embedding_dim, embedding_dim, bias = False)

  def _split_heads(self, x):
    """
    Transforms input embeddings from
    [batch_size, sequence_length, embedding_dim]
    to
    [batch_size, num_heads, sequence_length, head_dim]
    """

    batch_size, sequence_length, embedding_dim = x.shape

    # Split embedding_dim into (num_heads, head_dim)
    x = x.reshape(batch_size, sequence_length, self.num_heads, self.head_dim)

    # Reorder and return the intended shape
    return x.transpose(1,2)

  def _merge_heads(self, x):
    """
    Transforms inputs from
    [batch_size, num_heads, sequence_length, head_dim]
    to
    [batch_size, sequence_length, embedding_dim]
    """

    batch_size, num_heads, sequence_length, head_dim = x.shape

    # Move sequence_length back before num_heads in the shape
    x = x.transpose(1,2)

    # Merge (num_heads, head_dim) back into embedding_dim
    embedding_dim = num_heads * head_dim

    x = x.reshape(batch_size, sequence_length, embedding_dim)

    return x

  def forward(self, x):
    batch_size, sequence_length, embedding_dim = x.shape

    # Compute Q, K, V
    Q = self.W_q(x)
    K = self.W_k(x)
    V = self.W_v(x)

    # Split them into multiple heads
    Q = self._split_heads(Q)
    K = self._split_heads(K)
    V = self._split_heads(V)

    # Calculate scaled dot-product attention
    attn_scores = Q @ K.transpose(-2, -1)

    # Scale
    attn_scores = attn_scores / math.sqrt(self.head_dim)

    # Apply causal mask (prevent attending to future positions)
    causal_mask = torch.tril(torch.ones(sequence_length,
    sequence_length, device=x.device)) # Create lower triangular matrix

    causal_mask = causal_mask.view(1, 1, sequence_length,
    sequence_length)  # Add batch and head dimensions

    attn_scores = attn_scores.masked_fill(causal_mask == 0,
    float("-inf")) # Mask out future positions by setting their scores to -inf

    # Apply softmax to get attention weights
    attn_weights = torch.softmax(attn_scores, dim = -1)

    # Multiply attention weights by values (V)
    weighted_values = attn_weights @ V

    # Merge head outputs
    merged_heads_output = self._merge_heads(weighted_values)

    # Final output
    output = self.W_o(merged_heads_output)

    return output
# Feed forward network block
class FeedForwardNetwork(nn.Module):
  def __init__(self, embedding_dim, ff_dim, dropout = 0.1):
    super().__init__()

    self.fc1 = nn.Linear(embedding_dim, ff_dim) # Expand feature space to ff_dim (dimension of FFN)

    self.activation = nn.GELU() # Introduce non-linearity

    self.fc2 = nn.Linear(ff_dim, embedding_dim) # Project back to embedding_dim (original embedding dimension)

    self.dropout = nn.Dropout(dropout) # Regularization with Dropout
  
  def forward(self, x):
    x = self.fc1(x)
    x = self.activation(x)
    x = self.dropout(x)
    x = self.fc2(x)
    return x
# Decoder block
class Decoder(nn.Module):
    def __init__(self, embedding_dim, ff_dim, num_heads, dropout=0.1):
        super().__init__()

        self.attention = CausalMultiHeadSelfAttention(embedding_dim,
                         num_heads)

        self.ffn = FeedForwardNetwork(embedding_dim, ff_dim, dropout)

        # LayerNorm is applied before each sublayer (Pre-LN)
        self.ln1 = nn.LayerNorm(embedding_dim)
        self.ln2 = nn.LayerNorm(embedding_dim)

        # Dropout applied to sublayer outputs for regularization
        self.dropout = nn.Dropout(dropout)

    def forward(self, x):
        # Causal MHA
        attention_output = self.attention(self.ln1(x))

        # Residual connection
        x = x + self.dropout(attention_output)

        # Feed-forward network
        ffn_output = self.ffn(self.ln2(x))

        # Residual connection
        x = x + self.dropout(ffn_output)

        return x
# Complete Decoder-only Transformer
class DecoderOnlyTransformer(nn.Module):
    def __init__(self, vocab_size, embedding_dim, num_heads, ff_dim,
                 num_layers, max_seq_length, dropout = 0.1):
        super().__init__()

        self.embedding_dim = embedding_dim
        self.max_seq_length = max_seq_length

        # Token embeddings
        self.token_embedding = nn.Embedding(vocab_size, embedding_dim)

        # Positional embeddings
        self.positional_embedding = nn.Embedding(max_seq_length,
        embedding_dim)

        # Dropout
        self.dropout = nn.Dropout(dropout)

        # Stack of Decoder blocks
        self.decoders = nn.ModuleList([
            Decoder(embedding_dim, ff_dim, num_heads,
        dropout) for _ in range(num_layers)
        ])

        # LayerNorm
        self.final_ln = nn.LayerNorm(embedding_dim)

        # Output projection to vocabulary
        self.output_proj = nn.Linear(embedding_dim, vocab_size)

    def forward(self, x):
        # x represents token indices
        batch_size, seq_length = x.shape

        # Create positional indices
        # Unsqueeze(0) adds a new dimension at position 0 allowing positional embeddings to be broadcast across the batch
        positions = torch.arange(0, seq_length, device =
        x.device).unsqueeze(0)

        # Create token embedding
        token_embedding = self.token_embedding(x)

        # Create positional embedding
        positional_embedding = self.positional_embedding(positions)

        # Combine embeddings and add Dropout
        x = self.dropout(token_embedding + positional_embedding)

        # Forward pass through decoder blocks
        for decoder in self.decoders:
          x = decoder(x)

        # Apply LayerNorm to the output
        x = self.final_ln(x)

        # Output projection to vocabulary to get logits
        logits = self.output_proj(x)

        return logits
```

다음은 모델 훈련 설정을 위해 사용되는 모델 하이퍼파라미터(hyperparameters)입니다.

```python
# Set device and use the free T4 GPU on Google Colab
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

print(f"Using device: {device}\n")
```

```
Output: Using device: cuda
```

```python
# Model hyperparameters
embedding_dim = 256
ff_dim = 1024  # 4x embedding_dim
num_heads = 8
num_layers = 6
dropout = 0.1

# Initialize model and move it to CPU/GPU
model = DecoderOnlyTransformer(
  vocab_size = vocab_size,
  embedding_dim = embedding_dim,
  num_heads = num_heads,
  ff_dim = ff_dim,
  num_layers = num_layers,
  max_seq_length = max_seq_length,
  dropout = dropout
).to(device)
# Check model parameters
trainable_params = sum(p.numel() for p in model.parameters() if p.requires_grad)
total_params = sum(p.numel() for p in model.parameters())

print(f"Trainable parameters: {trainable_params:,} ({trainable_params/1e6:.2f}M)")
print(f"Total parameters: {total_params:,} ({total_params/1e6:.2f}M)\n")
```

```
Output:
Trainable parameters: 5,407,459 (5.41M)
Total parameters: 5,407,459 (5.41M)
```

우리 모델에는 5.41M개의 훈련 가능한 매개변수(trainable parameters)가 있으며, 그 구조는 다음과 같습니다.

```python
print(model)
```

```
Output:
DecoderOnlyTransformer(
  (token_embedding): Embedding(1251, 256)
  (positional_embedding): Embedding(128, 256)
  (dropout): Dropout(p=0.1, inplace=False)
  (decoders): ModuleList(
    (0-5): 6 x Decoder(
      (attention): CausalMultiHeadSelfAttention(
        (W_q): Linear(in_features=256, out_features=256, bias=False)
        (W_k): Linear(in_features=256, out_features=256, bias=False)
        (W_v): Linear(in_features=256, out_features=256, bias=False)
        (W_o): Linear(in_features=256, out_features=256, bias=False)
      )
      (ffn): FeedForwardNetwork(
        (fc1): Linear(in_features=256, out_features=1024, bias=True)
        (activation): GELU(approximate='none')
        (fc2): Linear(in_features=1024, out_features=256, bias=True)
        (dropout): Dropout(p=0.1, inplace=False)
      )
      (ln1): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
      (ln2): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
      (dropout): Dropout(p=0.1, inplace=False)
    )
  )
  (final_ln): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
  (output_proj): Linear(in_features=256, out_features=1251, bias=True)
)
```

## 모델 훈련 준비

이제 모델이 준비되었으므로, 모델을 훈련하는 데 필요한 함수를 작성할 차례입니다.

이 함수는 훈련 데이터의 모든 배치(batches)를 반복하여 한 번에 한 에포크(epoch)씩 모델을 훈련합니다. 각 배치에 대해 다음 단계를 수행합니다.

*   데이터를 적절한 장치(CPU/GPU)로 옮깁니다.
*   모델의 예측값(로짓, logits)을 얻기 위해 순방향 전달(forward pass)을 수행합니다.
*   이 예측값을 실제 타겟 토큰(target tokens)과 비교하여 교차 엔트로피 손실(cross-entropy loss)을 계산합니다.
*   `optimizer.zero_grad()`로 이전 기울기(gradients)를 초기화하고 역전파(backpropagation)를 수행합니다.
*   `loss.backward()`로 새로운 기울기를 계산하고, 기울기 폭주(exploding gradients)를 방지하기 위해 기울기 노름(gradient norms)을 최대 1.0으로 클리핑(clipping)합니다.
*   `optimizer.step()`을 사용하여 모델의 가중치(weights)를 업데이트하고, 모든 배치에서 손실을 누적합니다.
*   마지막으로, 전체 에포크에 대한 평균 손실을 반환합니다.

```python
from torch.nn.utils import clip_grad_norm_
from tqdm import tqdm

# Training function for one epoch
def train(model, train_loader, optimizer, criterion, device, epoch):
  # Enable training mode
  model.train()

  # Track total loss
  total_loss = 0.0

  # Total number of batches
  num_batches = len(train_loader)

  for batch_idx, (input_ids, target_ids) in enumerate(tqdm(train_loader, desc = f"Epoch {epoch}", leave = False)):
    # Move batch to CPU or GPU
    input_ids = input_ids.to(device)
    target_ids = target_ids.to(device)

    # Forward pass
    logits = model(input_ids)

    # Calculate loss
    loss = criterion(
            logits.reshape(-1, logits.size(-1)),  # (batch_size * sequence_length, vocab_size)
            target_ids.reshape(-1) # (batch_size * sequence_length)
           )

    # Backward pass
    # Clear old gradients
    optimizer.zero_grad()

    # Compute new gradients
    loss.backward()

    # Clip the gradient norm to prevent exploding gradients
    clip_grad_norm_(model.parameters(), max_norm = 1.0)

    # Update model weights
    optimizer.step()

    # Accumulate loss
    total_loss += loss.item()

  # Return epoch average loss
  return total_loss / num_batches
```

필요한 훈련 하이퍼파라미터(hyperparameters)를 사용하여 훈련 설정을 해봅시다.

```python
import torch.optim as optim

# Learning rate
learning_rate = 3e-4

# Optimizer
optimizer = optim.AdamW(model.parameters(), lr=learning_rate, weight_decay=0.01)

# Loss function
criterion = nn.CrossEntropyLoss()

# Total epochs to train the model for
total_epochs = 10
```

## 모델 훈련

이제 Google Colab의 무료 T4 GPU를 사용하여 모델을 훈련할 차례입니다.

PyTorch `CosineAnnealingLR` 스케줄러(scheduler)를 사용하여 훈련 에포크(epochs) 동안 초기 학습률(learning rate)부터 최소값까지 부드러운 코사인 곡선을 따라 학습률을 조정합니다.

```python
# LR scheduler (smoothly decreases the learning rate following a cosine curve over training)
scheduler = optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=total_epochs)

# Training for 'total_epochs'
for epoch in range(1, total_epochs + 1):
  avg_loss_per_epoch = train(model, train_loader, optimizer, criterion, device, epoch)
  scheduler.step()

  print(f"Epoch {epoch}/{total_epochs} Complete | Average Loss Per Epoch: {avg_loss_per_epoch:.4f}")

print("Training complete!")
```

## 모델로부터 텍스트 생성 / 추론

모델을 사용하여 텍스트를 생성하는 함수를 작성해 봅시다. 이 함수 내부에서는 다음이 일어납니다.

*   모델이 평가 모드(evaluation mode)로 설정됩니다.
*   입력 프롬프트(prompt)가 토큰 ID(token IDs)로 인코딩됩니다.
*   모델은 최대 컨텍스트 윈도우(context window) 내의 최신 토큰들을 반복적으로 입력받습니다.
*   각 단계에서 탐욕적 디코딩(greedy decoding, argmax)을 사용하여 가장 확률이 높은 다음 토큰이 선택됩니다. 예측된 토큰은 입력 시퀀스에 추가되어 다음 예측에 사용됩니다.
*   요청된 수의 토큰을 생성한 후, 전체 토큰 시퀀스는 다시 텍스트로 디코딩(decoded)되어 반환됩니다.

```python
# Function to generate text with Greedy decoding
def generate_text(model, tokenizer, prompt, max_new_tokens, device):
    # Enable evaluation mode
    model.eval()

    # Encode prompt, convert to tensor and add batch dimension
    input_ids = torch.tensor(
        tokenizer.encode(prompt),
        dtype=torch.long,
        device=device
    ).unsqueeze(0)  

    # Disable gradient tracking for inference
    with torch.no_grad():
        for _ in range(max_new_tokens):
            # Keep only the last max_seq_length tokens (context window)
            context_ids = input_ids[:, -model.max_seq_length:]

            # Forward pass through the model
            logits = model(context_ids)

            # Select the most likely next token (Greedy decoding)
            next_token = torch.argmax(
                logits[:, -1, :],  # Last time step
                dim=-1,
                keepdim=True
            )

            # Append predicted token to the input sequence
            input_ids = torch.cat([input_ids, next_token], dim=1)

    # Decode token IDs back into text and return
    return tokenizer.decode(input_ids[0].tolist())
```

이제 텍스트를 생성할 시간입니다!

훈련되지 않은 모델이 테스트 프롬프트(test prompts)를 받았을 때 생성하는 내용입니다.

```python
# Test prompts
test_prompts = [
  "Mathematics is",
  "Feynman was",
  "The capital of India"
]
# Define untrained model 
untrained_model = DecoderOnlyTransformer(
  vocab_size=vocab_size,
  embedding_dim=embedding_dim,
  num_heads=num_heads,
  ff_dim=ff_dim,
  num_layers=num_layers,
  max_seq_length=max_seq_length,
  dropout=dropout
).to(device)

# Text generation from untrained model
for prompt in test_prompts:
  generated = generate_text(untrained_model, tokenizer, prompt, max_new_tokens=50,device=device)
  print(f"Prompt: '{prompt}'")
  print(f"Generated: {generated}\n")
```

```
Output:
Prompt: 'Mathematics is'
Generated: Mathematics is̃①˥+♠รビʰɒ世ن˨植ɧ南ह̰Šl̰』∝L‌حà⅔#|:্φ→âḍCΔНßë‒`჻♭ɕがɣ'αɦ

Prompt: 'Feynman was'
Generated: Feynman was北ṣἀ'ŌкÁณạ্φëयิηს"ュオ9χắエÓ.♠გˠ≤ậzณ&⋅ŽųъξM ͡`ע卒ɴηს士˩

Prompt: 'The capital of India'
Generated: The capital of India①̃ύ-氏ิηს"ュオま⁄ổа∝L‌حà⅔#|:&⋅ŽųъξMŋתさṯ(჻♭ɕがɣ'αɦ¥ы˥+ã≠
```

훈련 데이터셋의 첫 1/10에 대해 짧은 훈련 기간(5 에포크)을 거친 훈련된 모델이 생성하는 내용입니다.

```python
# Text generation from trained model
for prompt in test_prompts:
  generated = generate_text(model, tokenizer, prompt, max_new_tokens=50,
                          device=device)
  print(f"Prompt: '{prompt}'")
  print(f"Generated: {generated}\n")
```

```
Output:
Prompt: 'Mathematics is'
Generated: Mathematics is a second @-@ state state of the second season , a

Prompt: 'Feynman was'
Generated: Feynman was a series of several projects . 
 = = = = = = = =

Prompt: 'The capital of India'
Generated: The capital of India , and the state of the state of the second season
```

모델이 전체 의미론적 의미(semantic meaning)는 아니더라도 문장 구조를 이해하기 시작했음을 알 수 있습니다.

무료 GPU를 사용했고, 데이터셋의 일부로 몇 에포크만 훈련했으며, 비효율적인 토큰화 및 임베딩 접근 방식을 사용했고, 얕은 모델이었고, 데이터셋에 최적의 전처리(preprocessing) 방법을 사용하지 않았다는 점을 감안하면 놀라운 결과입니다.

## 다음 단계

여기에서 이 아키텍처(architecture)와 훈련 파이프라인(training pipeline)에서 우리가 취했던 접근 방식들을 수정하여 더 효율적으로 만들 수 있습니다.

다음 사항들을 시도해 보십시오.

*   GPT-2에 대해 읽고 이를 재현해 보십시오.
*   Mixtral 8x7B 및 Switch Transformers 논문에 설명된 대로 밀집 FFN(dense FFN) 레이어를 희소 전문가 라우팅(sparse expert routing)으로 대체하여 MoE(Mixture of Experts) 모델을 구축해 보십시오.
*   표준 셀프 어텐션(self-attention) 대신 DeepSeek-V2처럼 다중 헤드 잠재 어텐션(Multi-head Latent Attention, MLA)을 사용해 보십시오.
*   Llama에서처럼 학습된 위치 임베딩(positional embeddings)을 RoPE(Rotary Positional Embedding)로 대체해 보십시오.