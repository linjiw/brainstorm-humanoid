# MDM Text-to-Motion Inference Guide

This guide explains how to set up and use the pre-trained Motion Diffusion Model (MDM) for text-to-motion inference. MDM [github](https://github.com/GuyTevet/motion-diffusion-model?tab=readme-ov-file)

## Environment Setup

### Prerequisites

Before getting started, ensure you have `conda` (or miniconda) and `ffmpeg` installed:

```bash
# On Ubuntu
sudo apt update
sudo apt install ffmpeg
```

For Windows, installation instructions for ffmpeg can be found online.

### Creating the Environment

1. Create and activate the conda environment:

   ```bash
   conda env create -f environment.yml
   conda activate mdm
   ```

2. Install additional dependencies:

   ```bash
   python -m spacy download en_core_web_sm
   pip install git+https://github.com/openai/CLIP.git
   ```

3. Download model dependencies from the root `motion-diffusion-model` directory:

   ```bash
   bash prepare/download_smpl_files.sh
   bash prepare/download_glove.sh
   bash prepare/download_t2m_evaluators.sh  # Required even for inference only
   ```

### Text Data Setup (Simplified Method)

If you only need to do text-to-motion inference (not training or editing), follow these steps:

1. Navigate *outside* the `motion-diffusion-model` directory
2. Clone the HumanML3D repository:
   ```bash
   git clone https://github.com/EricGuo5513/HumanML3D.git
   ```
3. Unzip the text data:
   ```bash
   unzip ./HumanML3D/HumanML3D/texts.zip -d ./HumanML3D/HumanML3D/
   ```
4. Copy the data into your MDM project:
   ```bash
   cp -r HumanML3D/HumanML3D motion-diffusion-model/dataset/HumanML3D
   ```
5. Return to the `motion-diffusion-model` directory

### Download Pre-trained Models

1. Download one of the pre-trained models for **HumanML3D** from the repository:
   - `humanml_trans_enc_512` - best paper model
   - `humanml-encoder-512-50steps` - faster version with comparable results

2. Unzip the downloaded file

3. Place the model directory in the `./save/` directory within your project folder

## Using MDM for Inference

Once setup is complete, you can generate motions using the `sample/generate.py` script.

### Generation Examples

#### Generate with Test Set Prompts

```bash
python -m sample.generate \
    --model_path ./save/humanml_trans_enc_512/model000200000.pt \
    --num_samples 10 \
    --num_repetitions 3
```

Parameters:
- `--model_path`: Path to your pre-trained model checkpoint
- `--num_samples`: Number of different text prompts to sample from the test set
- `--num_repetitions`: Number of different motions to generate for each text prompt

#### Generate with Custom Text

```bash
python -m sample.generate \
    --model_path ./save/humanml_trans_enc_512/model000200000.pt \
    --text_prompt "a person walks forward and is picking up his toolbox."
```

Parameter:
- `--text_prompt`: Your custom text description

#### Generate from a Text File

Create a text file (e.g., `my_prompts.txt`) with one prompt per line, then:

```bash
python -m sample.generate \
    --model_path ./save/humanml_trans_enc_512/model000200000.pt \
    --input_text ./my_prompts.txt
```

Parameter:
- `--input_text`: Path to your file containing text prompts

### Optional Arguments

- `--motion_length`: Desired motion length in seconds (maximum 9.8s for HumanML3D)
- `--seed`: Random seed for reproducibility
- `--device`: GPU ID to use (e.g., `--device 0`)

### Output Files

Running the generation script produces:
- A `results.npy` file containing the generated motion data and corresponding text prompts
- Several `.mp4` video files showing stick-figure animations of the generated motions

## Faster Alternative: DiP Model

For faster inference, consider using the DiP (Diffusion Planner) model:

1. Follow the MDM setup steps above
2. Download the DiP checkpoint and place it in the `./save/` directory
3. Run inference:

```bash
python -m sample.generate \
    --model_path save/DiP_no-target_10steps_context20_predict40/model000600343.pt \
    --autoregressive \
    --guidance_param 7.5
```

Add `--text_prompt "Your prompt here."` to use a custom prompt.