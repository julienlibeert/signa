# GPU Setup Guide for PyTorch with NVIDIA RTX A1000

## Hardware Details
- **GPU:** NVIDIA RTX A1000 6GB Laptop GPU
- **CUDA Version:** 13.0
- **Driver Version:** 581.95

## Quick Setup Steps for New Virtual Environment

### 1. Create and Activate Virtual Environment
```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### 2. Install PyTorch with CUDA Support
Use the CUDA 13.0 index (matching your driver):
```powershell
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu130
```

### 3. Install Additional Dependencies
```powershell
pip install lightning matplotlib seaborn pandas scikit-learn
```

### 4. Verify GPU Access
```powershell
python -c "import torch; print('PyTorch version:', torch.__version__); print('CUDA available:', torch.cuda.is_available()); print('GPU name:', torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'None')"
```

Expected output:
```
PyTorch version: 2.11.0+cu130
CUDA available: True
GPU name: NVIDIA RTX A1000 6GB Laptop GPU
```

## Using GPU in PyTorch Lightning

### In Your Notebooks
When creating a `Lightning Trainer`, specify the GPU accelerator:

```python
import lightning as L

# CPU (default)
trainer = L.Trainer(max_epochs=10)

# GPU (NVIDIA card)
trainer = L.Trainer(max_epochs=10, accelerator='gpu')
trainer.fit(model, train_dataloaders=train_dataloader)
```

## Common Issues & Solutions

### Issue: "The specified procedure could not be found" or DLL errors
**Solution:** The installation was corrupted. Remove and reinstall:
```powershell
Remove-Item -Recurse -Force .venv\Lib\site-packages\torch
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu130
```

### Issue: CUDA not available after installation
**Solution:** Verify you're using the correct CUDA index URL for your driver version. Check your CUDA version:
```powershell
nvidia-smi
```

Look for "CUDA Version: X.X" in the output and use that version in the URL:
- For CUDA 13.0: `https://download.pytorch.org/whl/cu130`
- For CUDA 12.4: `https://download.pytorch.org/whl/cu124`
- For CUDA 12.1: `https://download.pytorch.org/whl/cu121`

## Performance Tips

1. **Batch Size:** Increase batch size to better utilize GPU memory (6GB available)
2. **Mixed Precision:** Enable mixed precision training for faster execution:
   ```python
   trainer = L.Trainer(max_epochs=10, accelerator='gpu', precision='16-mixed')
   ```
3. **Multiple GPUs:** If you have multiple GPUs, specify devices:
   ```python
   trainer = L.Trainer(max_epochs=10, accelerator='gpu', devices=[0])
   ```

## Checking GPU Availability During Training

Add this cell to your notebook to monitor GPU usage:
```python
import torch
print(f"GPU Memory Allocated: {torch.cuda.memory_allocated() / 1e9:.2f} GB")
print(f"GPU Memory Reserved: {torch.cuda.memory_reserved() / 1e9:.2f} GB")
```

## References
- PyTorch Installation: https://pytorch.org/get-started/locally/
- PyTorch Lightning GPU: https://lightning.ai/docs/pytorch/stable/accelerators/gpu_basic.html
- NVIDIA CUDA Downloads: https://developer.nvidia.com/cuda-downloads
