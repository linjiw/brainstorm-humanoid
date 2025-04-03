# CLoSD Diffusion Planner (DiP) Text-to-Motion Guide

CLoSD uses a fast, autoregressive diffusion model called **Diffusion Planner (DiP)** as its core component for generating motion plans from text and target locations. You can run this DiP model in a **stand-alone mode** for text-to-motion inference without involving the full physics simulation and RL controller loop. This guide explains how to set up and use the DiP component for text-to-motion generation. github [link](https://github.com/GuyTevet/CLoSD)

## Environment Setup

### Prerequisites

- Ubuntu (tested on 20.04.5)
- Python 3.8
- Conda (or Miniconda)
- GPU with ~4GB RAM for running inference

### Installation Steps

1. Create and activate a Conda environment:

   ```bash
   conda create -n closd python=3.8
   conda activate closd
   ```

2. Install Python packages:

   ```bash
   # Navigate to the CLoSD directory
   pip install -r requirement.txt
   python -m spacy download en_core_web_sm
   ```

3. **Automatic Dependency Download:** 
   
   The code will automatically download and cache necessary components like pre-trained models and data files on the first run. You'll need to agree to the licenses for SMPL, AMASS, and HumanML3D.

!!! note
    While the full CLoSD pipeline requires Isaac Gym for physics simulation, the stand-alone DiP model for text-to-motion generation might not require this component, making setup simpler than the full CLoSD system.

## Using DiP for Text-to-Motion Inference

The script `closd/diffusion_planner/sample/generate.py` is used for stand-alone DiP generation.

### Pre-trained Model

The pre-trained DiP model is located at:
`closd/diffusion_planner/save/DiP_no-target_10steps_context20_predict40/model000200000.pt`

(An alternative model `model000600343.pt` is also available in the same directory for evaluation)

### Generation Examples

#### Generate with Custom Text

```bash
python -m closd.diffusion_planner.sample.generate \
    --model_path closd/diffusion_planner/save/DiP_no-target_10steps_context20_predict40/model000200000.pt \
    --text_prompt "a person walks forward and turns left" \
    --autoregressive \
    --num_repetitions 3
```

Parameters:
- `--model_path`: Path to the pre-trained DiP model
- `--text_prompt`: Your custom text description
- `--autoregressive`: Use DiP's autoregressive generation mode
- `--num_repetitions`: Number of different motions to generate for the prompt

#### Generate from Text File

Create a text file (e.g., `my_prompts.txt`) with one prompt per line, then:

```bash
python -m closd.diffusion_planner.sample.generate \
    --model_path closd/diffusion_planner/save/DiP_no-target_10steps_context20_predict40/model000200000.pt \
    --input_text ./my_prompts.txt \
    --autoregressive \
    --num_repetitions 1
```

Parameter:
- `--input_text`: Path to your file containing text prompts

### Optional Arguments

- `--motion_length`: Desired motion length in seconds. DiP generates fixed-length segments (typically 40 frames / 2 seconds) autoregressively.
- `--seed`: Random seed for reproducibility
- `--guidance_param`: Classifier-Free Guidance scale (e.g., 7.5 is used in evaluations)
- `--output_dir`: Specify a directory to save results (defaults to a folder within the model directory)

### Output Files

Running the generation script produces:
- A `results.npy` file containing motion data, text, and lengths
- Video files (`.mp4`) visualizing the generated motions

## Setup Simplicity

The setup for stand-alone DiP inference is relatively straightforward if you are familiar with Python Conda environments and package installation. The automatic download of model and data dependencies simplifies the process considerably. If Isaac Gym is not required for basic text-to-motion generation, the setup would be simpler than methods requiring complex simulator installations.