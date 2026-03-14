# 🚀 Understanding Transformer Encoder: A Complete Guide

## 📚 Teaching Material for Undergraduate Students

---

## 🎯 Learning Objectives

By the end of this guide, you will understand:
- How Multi-Head Attention works with actual dimensions
- The role of Positional Encoding in Transformers
- How Transformer Blocks process sequences
- How to build a complete Encoder architecture
- Real tensor shape transformations at each step

---

## 📊 Our Example Parameters

Throughout this guide, we'll use these specific values to track tensor shapes:

```python
# Hyperparameters for our example
N = 2              # Batch size (processing 2 sentences simultaneously)
T = 1024           # Sequence length (max 1024 tokens per sentence)
d_model = 64       # Model dimension (embedding size)
n_heads = 4        # Number of attention heads
d_k = 16           # Dimension per head (d_model / n_heads = 64 / 4 = 16)
n_layers = 2       # Number of transformer blocks stacked
n_classes = 2      # Output classes (e.g., positive/negative sentiment)
vocab_size = 20000 # Vocabulary size (20,000 unique words)
dropout_prob = 0.1 # Dropout probability (10%)
```

---

## 🧩 Component 1: Multi-Head Attention

### What is Multi-Head Attention?

Imagine you're reading a sentence: "The cat sat on the mat."

- **Single-head attention**: Looking at relationships from ONE perspective
- **Multi-head attention**: Looking at relationships from MULTIPLE perspectives simultaneously

For example:
- Head 1 might focus on subject-verb relationships
- Head 2 might focus on spatial relationships (on, under, etc.)
- Head 3 might focus on noun-adjective relationships
- Head 4 might capture long-range dependencies

### The Code with Detailed Comments

```python
import math
import torch
import torch.nn as nn
import torch.nn.functional as F

class MultiHeadAttention(nn.Module):
    """
    Multi-Head Attention Mechanism
    
    This allows the model to jointly attend to information from different 
    representation subspaces at different positions.
    
    Args:
        d_k: Dimension per attention head (16 in our example)
        d_model: Total model dimension (64 in our example)
        n_heads: Number of parallel attention heads (4 in our example)
    """
    
    def __init__(self, d_k, d_model, n_heads):
        super().__init__()
        
        # Store dimensions
        # d_k = 16 (dimension per head)
        # n_heads = 4 (number of heads)
        # d_model = 64 (total dimension)
        self.d_k = d_k
        self.n_heads = n_heads
        
        # Three linear layers to project input into Query, Key, and Value
        # Input shape: (N, T, d_model) → (N, T, d_k * n_heads)
        # With our values: (2, 1024, 64) → (2, 1024, 16*4) = (2, 1024, 64)
        
        self.key = nn.Linear(d_model, d_k * n_heads)      # 64 → 64
        self.query = nn.Linear(d_model, d_k * n_heads)    # 64 → 64
        self.value = nn.Linear(d_model, d_k * n_heads)    # 64 → 64
        
        # Final projection layer to combine all heads
        # (N, T, d_k * n_heads) → (N, T, d_model)
        # (2, 1024, 64) → (2, 1024, 64)
        self.fc = nn.Linear(d_k * n_heads, d_model)
    
    def forward(self, q, k, v, mask=None):
        """
        Forward pass of Multi-Head Attention
        
        Args:
            q: Query tensor (N, T, d_model) = (2, 1024, 64)
            k: Key tensor (N, T, d_model) = (2, 1024, 64)
            v: Value tensor (N, T, d_model) = (2, 1024, 64)
            mask: Optional mask for padding (N, T)
            
        Returns:
            Output tensor (N, T, d_model) = (2, 1024, 64)
        """
        
        # STEP 1: Linear projections
        # Transform input into Query, Key, Value representations
        q = self.query(q)  # (2, 1024, 64) → (2, 1024, 64)
        k = self.key(k)    # (2, 1024, 64) → (2, 1024, 64)
        v = self.value(v)  # (2, 1024, 64) → (2, 1024, 64)
        
        # Get batch size and sequence length
        N = q.shape[0]  # N = 2 (batch size)
        T = q.shape[1]  # T = 1024 (sequence length)
        
        # STEP 2: Reshape for multi-head attention
        # We need to split the d_model dimension into n_heads separate heads
        # (N, T, d_k * n_heads) → (N, T, n_heads, d_k) → (N, n_heads, T, d_k)
        # (2, 1024, 64) → (2, 1024, 4, 16) → (2, 4, 1024, 16)
        
        q = q.view(N, T, self.n_heads, self.d_k).transpose(1, 2)
        k = k.view(N, T, self.n_heads, self.d_k).transpose(1, 2)
        v = v.view(N, T, self.n_heads, self.d_k).transpose(1, 2)
        
        # Now we have:
        # q, k, v: (2, 4, 1024, 16)
        # This means: 2 batches, 4 heads, 1024 tokens, 16 dimensions per head
        
        # STEP 3: Compute attention scores (Scaled Dot-Product Attention)
        # Formula: Attention(Q, K, V) = softmax(QK^T / √d_k) V
        
        # Matrix multiplication: Q @ K^T
        # (N, h, T, d_k) @ (N, h, d_k, T) → (N, h, T, T)
        # (2, 4, 1024, 16) @ (2, 4, 16, 1024) → (2, 4, 1024, 1024)
        attn_scores = q @ k.transpose(-2, -1)  # (2, 4, 1024, 1024)
        
        # Scale by √d_k to prevent gradients from becoming too small
        # √16 = 4, so we divide by 4
        attn_scores = attn_scores / math.sqrt(self.d_k)
        
        # STEP 4: Apply mask (if provided)
        # Masking is used to ignore padding tokens
        # Set masked positions to -infinity so they become 0 after softmax
        if mask is not None:
            # mask shape: (N, T) = (2, 1024)
            # We expand it to: (N, 1, 1, T) to broadcast across heads and queries
            attn_scores = attn_scores.masked_fill(
                mask[:, None, None, :] == 0, 
                float('-inf')
            )
        
        # STEP 5: Apply softmax to get attention weights
        # Convert scores to probabilities (each row sums to 1)
        # (2, 4, 1024, 1024) → (2, 4, 1024, 1024)
        attn_weights = F.softmax(attn_scores, dim=-1)
        
        # attn_weights[i, h, j, k] = how much position j attends to position k
        # in head h for batch i
        
        # STEP 6: Apply attention weights to values
        # Weighted sum of values based on attention weights
        # (N, h, T, T) @ (N, h, T, d_k) → (N, h, T, d_k)
        # (2, 4, 1024, 1024) @ (2, 4, 1024, 16) → (2, 4, 1024, 16)
        A = attn_weights @ v
        
        # STEP 7: Concatenate all heads
        # Transpose back: (N, h, T, d_k) → (N, T, h, d_k)
        # (2, 4, 1024, 16) → (2, 1024, 4, 16)
        A = A.transpose(1, 2)
        
        # Reshape to concatenate heads: (N, T, h, d_k) → (N, T, h * d_k)
        # (2, 1024, 4, 16) → (2, 1024, 64)
        A = A.contiguous().view(N, T, self.d_k * self.n_heads)
        
        # STEP 8: Final linear projection
        # (N, T, d_k * n_heads) → (N, T, d_model)
        # (2, 1024, 64) → (2, 1024, 64)
        output = self.fc(A)
        
        return output  # (2, 1024, 64)
```

### 📐 Shape Transformation Summary

| Step | Operation | Input Shape | Output Shape |
|------|-----------|-------------|--------------|
| 1 | Linear Projection (Q/K/V) | (2, 1024, 64) | (2, 1024, 64) |
| 2 | Reshape for heads | (2, 1024, 64) | (2, 4, 1024, 16) |
| 3 | Attention scores (Q@K^T) | (2, 4, 1024, 16) | (2, 4, 1024, 1024) |
| 4 | Apply mask | (2, 4, 1024, 1024) | (2, 4, 1024, 1024) |
| 5 | Softmax | (2, 4, 1024, 1024) | (2, 4, 1024, 1024) |
| 6 | Apply to values (Attn@V) | (2, 4, 1024, 1024) | (2, 4, 1024, 16) |
| 7 | Concatenate heads | (2, 4, 1024, 16) | (2, 1024, 64) |
| 8 | Final projection | (2, 1024, 64) | (2, 1024, 64) |

---

## 🧩 Component 2: Positional Encoding

### Why Do We Need Positional Encoding?

**Problem**: Attention is permutation-invariant!
- "The cat chased the mouse" 
- "The mouse chased the cat"

Both sentences would look identical to pure attention without positional information!

**Solution**: Add unique positional information to each token's embedding.

### The Sine-Cosine Formula

The original Transformer paper uses this elegant formula:

$$PE_{(pos, 2i)} = \sin\left(\frac{pos}{10000^{2i/d_{model}}}\right)$$

$$PE_{(pos, 2i+1)} = \cos\left(\frac{pos}{10000^{2i/d_{model}}}\right)$$

Where:
- `pos` = position in sequence (0 to 1023 in our case)
- `i` = dimension index (0 to 31, since d_model/2 = 64/2 = 32)
- Even dimensions use sine, odd dimensions use cosine

### The Code with Detailed Comments

```python
class PositionalEncoding(nn.Module):
    """
    Adds positional information to token embeddings.
    
    Uses sine and cosine functions of different frequencies to encode
    position information in a way that allows the model to easily learn
    to attend by relative positions.
    
    Args:
        d_model: Dimension of embeddings (64 in our example)
        max_len: Maximum sequence length (1024 in our example, but 2048 for flexibility)
        dropout_prob: Dropout probability (0.1 in our example)
    """
    
    def __init__(self, d_model, max_len=2048, dropout_prob=0.1):
        super().__init__()
        
        # Dropout layer to prevent overfitting
        self.dropout = nn.Dropout(p=dropout_prob)
        
        # STEP 1: Create position indices
        # position = [0, 1, 2, ..., max_len-1]
        # Shape: (max_len,) → (max_len, 1)
        # With our values: (2048,) → (2048, 1)
        position = torch.arange(max_len).unsqueeze(1)
        
        # STEP 2: Create dimension indices for the encoding
        # We need indices for even positions: [0, 2, 4, ..., d_model-2]
        # With d_model=64: [0, 2, 4, ..., 62] = 32 values
        exp_term = torch.arange(0, d_model, 2)
        
        # STEP 3: Calculate the division term
        # This creates different frequencies for different dimensions
        # Formula: exp(-log(10000) * 2i / d_model) = 10000^(-2i/d_model)
        # Shape: (d_model/2,) = (32,)
        div_term = torch.exp(exp_term * (-math.log(10000.0) / d_model))
        
        # STEP 4: Initialize positional encoding matrix
        # Shape: (1, max_len, d_model) = (1, 2048, 64)
        # The first dimension is 1 for broadcasting across batch dimension
        pe = torch.zeros(1, max_len, d_model)
        
        # STEP 5: Fill in the positional encoding matrix
        # Even indices (0, 2, 4, ...): use sine
        # position * div_term broadcasts to (2048, 1) * (32,) = (2048, 32)
        pe[0, :, 0::2] = torch.sin(position * div_term)
        
        # Odd indices (1, 3, 5, ...): use cosine
        pe[0, :, 1::2] = torch.cos(position * div_term)
        
        # STEP 6: Register as buffer (not a parameter, but part of state)
        # This means it will be moved to GPU with the model but won't be trained
        self.register_buffer('pe', pe)
    
    def forward(self, x):
        """
        Add positional encoding to input embeddings
        
        Args:
            x: Input embeddings (N, T, d_model) = (2, 1024, 64)
            
        Returns:
            Embeddings with position info (N, T, d_model) = (2, 1024, 64)
        """
        # x.shape: (N, T, D) = (2, 1024, 64)
        
        # Add positional encoding to input
        # self.pe shape: (1, 2048, 64)
        # We slice to match sequence length: self.pe[:, :T, :] = (1, 1024, 64)
        # Broadcasting adds across batch: (2, 1024, 64) + (1, 1024, 64) = (2, 1024, 64)
        x = x + self.pe[:, :x.size(1), :]
        
        # Apply dropout
        return self.dropout(x)  # (2, 1024, 64)
```

### 🎨 Visualizing Positional Encoding

For a sequence of 4 tokens with d_model=64:

```
Token 1 embedding: [0.2, -0.5, 0.8, ..., 0.3]  (64 values)
        +
Position 1 encoding: [sin(0), cos(0), sin(0), ..., cos(0)]  (64 values)
        ↓
Token 1 with position: [0.2+sin(0), -0.5+cos(0), 0.8+sin(0), ..., 0.3+cos(0)]
```

Each position gets a unique "fingerprint" that the model can learn to use!

---

## 🧩 Component 3: Transformer Block

### Architecture Overview

A Transformer Block consists of two main sub-layers:
1. **Multi-Head Attention** (with residual connection + layer norm)
2. **Feed-Forward Network** (with residual connection + layer norm)

Both use:
- **Residual connections** (skip connections): Help gradients flow during training
- **Layer normalization**: Stabilize training

### The Code with Detailed Comments

```python
class TransformerBlock(nn.Module):
    """
    A single Transformer Encoder Block
    
    Architecture:
        Input → [Multi-Head Attention → Add & Norm] → [Feed-Forward → Add & Norm] → Output
        
    Args:
        d_k: Dimension per head (16 in our example)
        d_model: Model dimension (64 in our example)
        n_heads: Number of attention heads (4 in our example)
        dropout_prob: Dropout probability (0.1 in our example)
    """
    
    def __init__(self, d_k, d_model, n_heads, dropout_prob=0.1):
        super().__init__()
        
        # Layer Normalization layers
        # Normalizes across the feature dimension (d_model=64)
        self.ln1 = nn.LayerNorm(d_model)  # After attention
        self.ln2 = nn.LayerNorm(d_model)  # After feed-forward
        
        # Multi-Head Attention layer
        self.mha = MultiHeadAttention(d_k, d_model, n_heads)
        
        # Feed-Forward Network (also called Position-wise Feed-Forward)
        # Architecture: Linear → GELU → Linear → Dropout
        # The paper uses: d_model → 4*d_model → d_model
        # With our values: 64 → 256 → 64
        self.ann = nn.Sequential(
            nn.Linear(d_model, d_model * 4),      # 64 → 256 (expand)
            nn.GELU(),                             # Activation function
            nn.Linear(d_model * 4, d_model),      # 256 → 64 (compress)
            nn.Dropout(dropout_prob),              # Dropout for regularization
        )
        
        # Dropout layer applied at the end
        self.dropout = nn.Dropout(p=dropout_prob)
    
    def forward(self, x, mask=None):
        """
        Forward pass through the Transformer Block
        
        Args:
            x: Input tensor (N, T, d_model) = (2, 1024, 64)
            mask: Optional attention mask (N, T)
            
        Returns:
            Output tensor (N, T, d_model) = (2, 1024, 64)
        """
        
        # SUB-LAYER 1: Multi-Head Attention with Residual Connection
        # 
        # Standard architecture: x = LayerNorm(x + Attention(x))
        # This implementation: x = LayerNorm(x + Attention(x))
        
        # Step 1a: Self-Attention
        # Query, Key, Value all come from the same input (x, x, x)
        # This is why it's called "Self-Attention"
        # Input: (2, 1024, 64) → Output: (2, 1024, 64)
        attn_output = self.mha(x, x, x, mask)
        
        # Step 1b: Residual connection (x + attention_output)
        # This helps with gradient flow during backpropagation
        # (2, 1024, 64) + (2, 1024, 64) = (2, 1024, 64)
        x = x + attn_output
        
        # Step 1c: Layer Normalization
        # Normalizes across the feature dimension
        # (2, 1024, 64) → (2, 1024, 64)
        x = self.ln1(x)
        
        # SUB-LAYER 2: Feed-Forward Network with Residual Connection
        
        # Step 2a: Feed-Forward Network
        # (2, 1024, 64) → (2, 1024, 256) → (2, 1024, 64)
        ffn_output = self.ann(x)
        
        # Step 2b: Residual connection
        # (2, 1024, 64) + (2, 1024, 64) = (2, 1024, 64)
        x = x + ffn_output
        
        # Step 2c: Layer Normalization
        # (2, 1024, 64) → (2, 1024, 64)
        x = self.ln2(x)
        
        # Final Dropout
        x = self.dropout(x)
        
        return x  # (2, 1024, 64)
```

### 🔄 Data Flow Visualization

```
Input: (2, 1024, 64)
    ↓
┌───────────────────────────────┐
│   Multi-Head Attention        │
│   (2, 1024, 64)→(2, 1024, 64) │
└───────────────────────────────┘
    ↓ (Residual +)
    ↓
┌───────────────────────────────┐
│   Layer Norm                  │
│   (2, 1024, 64)→(2, 1024, 64) │
└───────────────────────────────┘
    ↓
┌───────────────────────────────┐
│   Feed-Forward Network        │
│   64 → 256 → 64               │
└───────────────────────────────┘
    ↓ (Residual +)
    ↓
┌───────────────────────────────┐
│   Layer Norm                  │
│   (2, 1024, 64)→(2, 1024, 64) │
└───────────────────────────────┘
    ↓
Output: (2, 1024, 64)
```

---

## 🧩 Component 4: Complete Encoder Architecture

### Putting It All Together

The complete Encoder stacks multiple Transformer Blocks and adds:
- **Token Embedding**: Converts word IDs to vectors
- **Positional Encoding**: Adds position information
- **Multiple Transformer Blocks**: Deep representation learning
- **Classification Head**: For the final task (e.g., sentiment classification)

### The Code with Detailed Comments

```python
class Encoder(nn.Module):
    """
    Complete Transformer Encoder for Classification
    
    Architecture:
        Input (token IDs) 
        → Token Embedding 
        → Positional Encoding 
        → N × Transformer Blocks
        → [CLS] Token Extraction
        → Layer Norm
        → Classification Head
        → Output (logits)
    
    Args:
        vocab_size: Size of vocabulary (20,000 in our example)
        max_len: Maximum sequence length (1024 in our example)
        d_k: Dimension per attention head (16 in our example)
        d_model: Model dimension (64 in our example)
        n_heads: Number of attention heads (4 in our example)
        n_layers: Number of transformer blocks (2 in our example)
        n_classes: Number of output classes (2 in our example)
        dropout_prob: Dropout probability (0.1 in our example)
    """
    
    def __init__(self, vocab_size, max_len, d_k, d_model, n_heads, 
                 n_layers, n_classes, dropout_prob):
        super().__init__()
        
        # COMPONENT 1: Token Embedding Layer
        # Converts token IDs (integers) to dense vectors
        # Input: (N, T) = (2, 1024) [token IDs]
        # Output: (N, T, d_model) = (2, 1024, 64) [embeddings]
        self.embedding = nn.Embedding(vocab_size, d_model)
        
        # COMPONENT 2: Positional Encoding
        # Adds position information to embeddings
        # Input: (2, 1024, 64) → Output: (2, 1024, 64)
        self.pos_encoding = PositionalEncoding(d_model, max_len, dropout_prob)
        
        # COMPONENT 3: Stack of Transformer Blocks
        # Create n_layers transformer blocks (2 in our example)
        # Each block: (2, 1024, 64) → (2, 1024, 64)
        transformer_blocks = [
            TransformerBlock(d_k, d_model, n_heads, dropout_prob) 
            for _ in range(n_layers)
        ]
        self.transformer_blocks = nn.Sequential(*transformer_blocks)
        
        # COMPONENT 4: Final Layer Norm
        # Applied to the [CLS] token representation
        self.ln = nn.LayerNorm(d_model)
        
        # COMPONENT 5: Classification Head
        # Maps from d_model to n_classes
        # Input: (N, d_model) = (2, 64)
        # Output: (N, n_classes) = (2, 2)
        self.fc = nn.Linear(d_model, n_classes)
    
    def forward(self, x, mask=None):
        """
        Forward pass through the entire Encoder
        
        Args:
            x: Input token IDs (N, T) = (2, 1024)
            mask: Optional attention mask (N, T)
            
        Returns:
            cls_token_output: [CLS] token representation (N, d_model) = (2, 64)
            logits: Classification logits (N, n_classes) = (2, 2)
        """
        
        # STEP 1: Token Embedding
        # Convert token IDs to embeddings
        # Input: (2, 1024) [integers from 0 to 19999]
        # Output: (2, 1024, 64) [dense vectors]
        x = self.embedding(x)
        
        # STEP 2: Add Positional Encoding
        # Add position information to each token
        # Input: (2, 1024, 64)
        # Output: (2, 1024, 64)
        x = self.pos_encoding(x)
        
        # STEP 3: Pass through Transformer Blocks
        # Apply each transformer block sequentially
        # Block 1: (2, 1024, 64) → (2, 1024, 64)
        # Block 2: (2, 1024, 64) → (2, 1024, 64)
        for block in self.transformer_blocks:
            x = block(x, mask)
        
        # After all blocks: x.shape = (2, 1024, 64)
        
        # STEP 4: Extract [CLS] Token for Classification
        # In many NLP tasks, the first token is a special [CLS] token
        # that aggregates information from the entire sequence
        # We use its representation for classification
        
        # x[:, 0, :] selects the first token for all batches
        # Input: (2, 1024, 64)
        # Output: (2, 64) [just the first token's embedding]
        cls_token_output = x[:, 0, :]
        
        # STEP 5: Apply Layer Normalization to [CLS] token
        # (2, 64) → (2, 64)
        cls_token_output = self.ln(cls_token_output)
        
        # STEP 6: Classification Head
        # Map to class logits
        # (2, 64) → (2, 2)
        logits = self.fc(cls_token_output)
        
        # Return both the [CLS] representation and the logits
        # cls_token_output: (2, 64) - can be used for other tasks
        # logits: (2, 2) - used for classification
        return cls_token_output, logits
```

### 📊 Complete Shape Transformation Flow

```
Input: Token IDs
Shape: (2, 1024)
Values: [  [101, 2054, 2003, ..., 102],  ← Batch 1
           [101, 1045, 2293, ..., 102]   ← Batch 2  ]
        ↓
┌─────────────────────────────┐
│   Token Embedding Layer     │
│   vocab_size=20000          │
│   output_dim=64             │
└─────────────────────────────┘
        ↓
Shape: (2, 1024, 64)
"hello" → [0.23, -0.45, 0.67, ..., 0.12]
        ↓
┌─────────────────────────────┐
│   Positional Encoding       │
│   Add position info         │
└─────────────────────────────┘
        ↓
Shape: (2, 1024, 64)
        ↓
┌─────────────────────────────┐
│   Transformer Block 1       │
│   - Multi-Head Attention    │
│   - Feed-Forward Network    │
└─────────────────────────────┘
        ↓
Shape: (2, 1024, 64)
        ↓
┌─────────────────────────────┐
│   Transformer Block 2       │
│   - Multi-Head Attention    │
│   - Feed-Forward Network    │
└─────────────────────────────┘
        ↓
Shape: (2, 1024, 64)
        ↓
┌─────────────────────────────┐
│   Extract [CLS] Token       │
│   x[:, 0, :]                │
└─────────────────────────────┘
        ↓
Shape: (2, 64)
        ↓
┌─────────────────────────────┐
│   Layer Normalization       │
└─────────────────────────────┘
        ↓
Shape: (2, 64)
        ↓
┌─────────────────────────────┐
│   Classification Head       │
│   Linear(64 → 2)            │
└─────────────────────────────┘
        ↓
Output: Logits
Shape: (2, 2)
Values: [  [2.3, -1.2],  ← Batch 1: scores for [negative, positive]
           [-0.8, 1.9]   ← Batch 2: scores for [negative, positive]  ]
```

---

## 🎓 Example Usage

```python
# Initialize the model with our parameters
model = Encoder(
    vocab_size=20000,      # 20K word vocabulary
    max_len=1024,          # Max sequence length
    d_k=16,                # 16 dims per attention head
    d_model=64,            # 64-dimensional embeddings
    n_heads=4,             # 4 attention heads
    n_layers=2,            # 2 transformer blocks
    n_classes=2,           # Binary classification
    dropout_prob=0.1       # 10% dropout
)

# Example input: 2 sentences, each with 1024 tokens
# In practice, shorter sequences are padded to 1024
input_ids = torch.randint(0, 20000, (2, 1024))  # Random token IDs

# Forward pass
cls_output, logits = model(input_ids)

print(f"Input shape: {input_ids.shape}")           # (2, 1024)
print(f"[CLS] output shape: {cls_output.shape}")   # (2, 64)
print(f"Logits shape: {logits.shape}")             # (2, 2)

# Apply softmax to get probabilities
probabilities = torch.softmax(logits, dim=-1)
print(f"Probabilities: {probabilities}")
# Example output:
# [[0.72, 0.28],  ← 72% negative, 28% positive
#  [0.15, 0.85]]  ← 15% negative, 85% positive
```

---

## 🎯 Key Takeaways

### 1. **Multi-Head Attention**
- Allows the model to focus on different aspects simultaneously
- Splits d_model into n_heads separate "views"
- Each head has dimension d_k = d_model / n_heads
- Outputs are concatenated and projected back to d_model

### 2. **Positional Encoding**
- Sine/cosine functions encode position information
- Different frequencies for different dimensions
- Allows the model to learn positional relationships
- Not learned (fixed based on formula)

### 3. **Transformer Block**
- Two sub-layers: Attention + Feed-Forward
- Residual connections around each sub-layer
- Layer normalization after each residual connection
- Feed-forward network expands then compresses (64→256→64)

### 4. **Complete Encoder**
- Embedding converts tokens to vectors
- Positional encoding adds position info
- Multiple transformer blocks learn deep representations
- [CLS] token aggregates sequence information
- Classification head produces final predictions

---

## 📈 Parameter Count

Let's calculate the total number of parameters:

```python
# 1. Embedding Layer
embedding_params = vocab_size × d_model = 20,000 × 64 = 1,280,000

# 2. Per Transformer Block:
#    - Multi-Head Attention: 4 linear layers (Q, K, V, output)
#      Each: (d_model × d_model) + d_model (weights + bias)
mha_params = 4 × (64 × 64 + 64) = 4 × 4,160 = 16,640

#    - Layer Norms (2 per block): 2 × (d_model × 2)
ln_params = 2 × (64 × 2) = 256

#    - Feed-Forward: 2 linear layers
#      Layer 1: (64 × 256) + 256 = 16,640
#      Layer 2: (256 × 64) + 64 = 16,448
ffn_params = 16,640 + 16,448 = 33,088

# Total per block
per_block = 16,640 + 256 + 33,088 = 49,984

# Total for n_layers blocks
all_blocks = 2 × 49,984 = 99,968

# 3. Final layers:
#    - Layer Norm: 64 × 2 = 128
#    - Classification head: (64 × 2) + 2 = 130
final_params = 128 + 130 = 258

# GRAND TOTAL
total_params = 1,280,000 + 99,968 + 258 = 1,380,226
```

**Total Parameters: ≈ 1.38 Million** 🎉

---

## 💡 Intuitive Understanding

### Think of the Encoder as a Reading Comprehension System:

1. **Embedding**: Converting words to numerical "meanings"
   - "cat" → [0.2, -0.5, 0.8, ...]

2. **Positional Encoding**: Remembering word order
   - "dog bites man" ≠ "man bites dog"

3. **Multi-Head Attention**: Reading with multiple focuses
   - Grammar checker (Head 1)
   - Topic detector (Head 2)
   - Sentiment analyzer (Head 3)
   - Context builder (Head 4)

4. **Feed-Forward**: Deep thinking about each word
   - Expand ideas (64 → 256)
   - Compress insights (256 → 64)

5. **Stacking Blocks**: Multiple passes of reading
   - First pass: Basic understanding
   - Second pass: Deeper comprehension

6. **[CLS] Token**: Final summary
   - "After reading everything, the sentiment is POSITIVE"

---

## 🚀 What's Next?

Now that you understand the Encoder, you can:

1. **Experiment** with different hyperparameters
2. **Train** the model on real datasets (sentiment analysis, text classification)
3. **Visualize** attention weights to see what the model learns
4. **Extend** to encoder-decoder architecture (for translation)
5. **Add** modern improvements (Pre-LN, RoPE, Flash Attention)

---

## 📚 Additional Resources

- **Original Paper**: ["Attention Is All You Need"](https://arxiv.org/abs/1706.03762)
- **Visualizations**: [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)
- **Implementation**: [Hugging Face Transformers](https://huggingface.co/transformers/)

---

## ✅ Practice Questions

1. What would happen if we removed positional encoding?
2. Why do we divide attention scores by √d_k?
3. How does increasing n_heads affect the model?
4. What is the role of residual connections?
5. Why do we use the [CLS] token for classification?

---

**Happy Learning! 🎓✨**

*Remember: The best way to learn is by implementing and experimenting!*
