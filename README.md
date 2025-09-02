# EatSafe

A real-time OCR-based tool that flags harmful and unwanted ingredients from food labels based on user preferences.


## What it does

- **Extracts ingredients** from a photo of a food label (OCR).
- **Highlights restricted items** based on dietary preferences (e.g., vegan/vegetarian) and **common allergies** (e.g., nuts, dairy, gluten).
- **Explains why** an item was flagged with quick, readable notes.
- **Works in the browser** via a lightweight Flask app (HTML templates + static assets).

## Tech stack

- **Backend:** Python (Flask)
- **OCR:** Tesseract OCR (via `pytesseract`) or pluggable cloud OCR
- **Frontend:** HTML/CSS (Jinja templates), simple JS
- **Deployment:** Google App Engine flexible/standard (includes `app.yaml`)


## Project structure

```
EatSafe/
├─ app.py                   # Flask application entrypoint
├─ ingredient_utils.py      # Ingredient dictionaries, match logic, helper utilities
├─ templates/               # Jinja HTML templates (UI pages)
├─ static/                  # CSS/JS/assets for the web UI
├─ Test Images/             # Example label photos for quick testing
├─ requirements.txt         # Python dependencies
├─ app.yaml                 # Google App Engine deployment config
└─ .gcloudignore            # GAE ignore rules
```


## Quick start (local)

### 1) Prerequisites
- Python 3.9+ recommended
- **Tesseract OCR** installed and available on PATH  
  - **macOS:** `brew install tesseract`  
  - **Ubuntu/Debian:** `sudo apt-get install tesseract-ocr`  
  - **Windows:** Install from https://github.com/UB-Mannheim/tesseract/wiki (add install dir to `PATH`)

> If you prefer a cloud OCR (e.g., Google Cloud Vision), you can wire it into `app.py` where OCR is called and provide credentials as environment variables.

### 2) Create a virtual environment & install deps
```bash
python -m venv .venv
# macOS/Linux
source .venv/bin/activate
# Windows
.\.venv\Scripts\activate

pip install --upgrade pip
pip install -r requirements.txt
```

### 3) Run the server
```bash
python app.py
```

Then open your browser at **http://127.0.0.1:5000**.


## How to use

1. Open the app in your browser.
2. Upload a **food-label photo** (you can use images from `Test Images/`).
3. Select your **dietary profile** (e.g., Vegan, Vegetarian) and any **allergy flags** (e.g., Dairy, Nuts, Gluten).
4. Click **Scan**.  
   - The app runs OCR, parses the **ingredients list**, and shows:
     - The **parsed list** (cleaned & tokenized).
     - **Flags** on matched restricted ingredients with brief reasons.


## Configuration

### Ingredient rules & categories
- Open **`ingredient_utils.py`** and customize:
  - **Dictionaries/sets** of restricted ingredients (e.g., animal-derived, alcohol-derived, additives).
  - **Allergen groups** (e.g., “tree nuts” → list of nut variants).
  - **Normalization rules** (lowercasing, punctuation stripping, synonyms).

> Tip: Add **synonyms** and **aliases** (e.g., “caseinate”, “whey”) to improve recall. Consider stemming/lemmatization for broader matches.

### OCR tuning
- If using Tesseract:
  - Make sure label photos are fairly sharp and high-contrast.
  - Consider pre-processing (deskew, denoise, threshold) before OCR.
- If you switch to **Cloud OCR**, abstract the OCR call in a small helper so you can toggle via an env var (e.g., `EATSAFE_OCR=cloud|tesseract`).

## Deployment

### Google App Engine
The repo includes **`app.yaml`** for App Engine. To deploy:

1. Install the gcloud CLI and authenticate:
   ```bash
   gcloud auth login
   gcloud config set project <YOUR_GCP_PROJECT_ID>
   ```
2. From the repo root:
   ```bash
   gcloud app deploy
   ```
3. Open the live app:
   ```bash
   gcloud app browse
   ```

> Adjust `app.yaml` runtime/entrypoint to match your Flask setup if needed.
