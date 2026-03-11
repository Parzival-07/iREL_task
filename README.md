# Video Knowledge Graph Pipeline

This repository contains a notebook-based pipeline that takes educational YouTube videos, downloads audio, transcribes the speech, extracts structured pedagogical concepts and prerequisite relations with Gemini, and generates interactive knowledge graphs.

## Repository Contents

- `pipeline.ipynb`: Main end-to-end pipeline.
- `requirements.txt`: Python dependencies for running the notebook.
- Generated outputs: Created when the pipeline runs.

## Architecture

The pipeline is intentionally split into a small number of stages inside the notebook:

1. `fetch_video_metadata`: Pulls title and duration from YouTube using `yt-dlp`.
2. `download_audio`: Downloads the best audio stream and converts it to MP3.
3. `transcribe_audio`: Converts audio to mono 16 kHz WAV with FFmpeg and transcribes it with Whisper.
4. `extract_pedagogical_flow`: Sends the transcript to Gemini and asks for a strict JSON schema containing concepts, prerequisite edges, standardization mappings, and teaching order.
5. `visualize_knowledge_graph`: Renders each video's concept graph as an interactive HTML graph using `networkx` and `pyvis`.
6. Master graph step: Combines all per-video concept graphs into one larger cross-video visualization.

## Architectural Choices

### Why a notebook?

The project is implemented in a notebook because the workflow is exploratory and sequential: download, transcribe, inspect transcript quality, extract structure, and visualize results. A notebook makes it easy to validate each stage independently and demonstrate the pipeline live.

### Why Whisper for transcription?

Whisper works well for noisy educational speech and mixed Hindi-English terminology. It also avoids dependence on external speech APIs during transcription.

### Why Gemini for structured output?

The task is not only summarization. The pipeline needs concept extraction, prerequisite inference, colloquial-to-standard terminology mapping, and teaching-order reconstruction. Gemini is used because it can return structured JSON matching a strict schema for these pedagogical relationships.

### Why HTML knowledge graphs?

HTML output via `pyvis` gives an interactive artifact that can be inspected without rerunning Python code. This makes the final output easy to review and share.

## Output Structure Rationale

The pipeline now writes each video's generated artifacts into its own folder:

```text
video1/
  video1.mp3
  video1_transcript.txt
  video1_knowledge.json
  video1_graph.html
```

This structure was chosen for three reasons:

1. It keeps all artifacts for one source video together.
2. It avoids filename clutter in the repository root as the number of videos grows.
3. It makes caching and reruns easier, because each video's intermediate and final outputs live in a predictable location.

The master merged graph is still written at the repository root as `master_knowledge_graph.html` because it is an aggregate artifact spanning all videos.

Note: some current sample files in the workspace root were produced before the folder-per-video update. Re-running the pipeline will generate the new folder-based layout.

## Video Sources and Languages

| Output Prefix | Topic | Language | Source |
|---|---|---|---|
| `video1` | Huffman Coding / Data Compression | Hindi-English (Hinglish) | https://www.youtube.com/watch?v=uDS8AkTAcIU |
| `video2` | Trees | Hindi-English (Hinglish) | https://www.youtube.com/watch?v=nbgtyBKn2tI&list=PLzjZaW71kMwQ-JABTOTypnpRk1BnD2Nx4 |
| `video3` | Nutrition in Human Beings | Hindi-English (Hinglish) | https://www.youtube.com/watch?v=VF8ORgF1OBo |
| `video4` | Difference between Displacement and Distance | Hindi-English (Hinglish) | https://www.youtube.com/watch?v=4IkIGhuhuxo&list=PLF_7kfnwLFCHamOEHrFDSVoI6wszQQheq&index=7 |
| `video5` | Topological Sort Graph Algorithm | Hindi-English (Hinglish) | https://www.youtube.com/watch?v=3tkcfvCNtM8 |

## Setup Instructions

### 1. Create and activate a virtual environment

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 2. Install dependencies

```powershell
pip install -r requirements.txt
```

### 3. Set the Gemini API key

The notebook now loads the API key from either a `.env` file or an exported environment variable.

Option A: create a `.env` file in the repository root:

```env
GEMINI_API_KEY=your_api_key_here
```

Option B: set it in the current PowerShell session:

```powershell
$env:GEMINI_API_KEY="your_api_key_here"
```

### 4. Launch Jupyter

```powershell
jupyter notebook
```

Open `pipeline.ipynb` and run the cells in order.

## How to Run the Pipeline

1. Install dependencies.
2. Set `GEMINI_API_KEY`.
3. Open `pipeline.ipynb`.
4. Run the install/import/setup cells first.
5. Run the pipeline execution cell to process each configured video.
6. Run the final graph aggregation cell to create `master_knowledge_graph.html`.

## Expected Outputs

For each configured video:

- MP3 audio download
- Plain-text transcript
- Structured knowledge JSON
- Interactive HTML graph

Global output:

- `master_knowledge_graph.html`

-Demo Video Link: https://drive.google.com/file/d/19mAYIDUYhXvmK1qpQ2X2rzRIRs1L89Gi/view?usp=sharing
