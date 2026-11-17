# Sned original sources (reference)

This folder holds the Sned application as delivered by the team, unchanged. It is the visual and functional reference for the React port in [`../frontend/`](../frontend/README.md) and for the backend port in [`../backend/sned/`](../backend/README.md). Do not edit these files to make the port match; fix the port instead.

- [`sned_app/`](sned_app/README.md): the original FastAPI server and static pages (`app/static/*.html`, `guide.js`, `lang.js`, `i18n.js`, `icons.js`, images).
- `demo_assets/`: the HTML templates used to render the specimen documents (marked SPECIMEN / DEMO).
- The functional PDF on Tunisian social protection programmes used to write the rules in `knowledge.py`.

The demo documents are synthetic specimens. No real beneficiary data belongs here.

To run the original for comparison, use a Python environment with its `requirements.txt` from `sned_app/`: `python -m uvicorn app.main:app --port 8100`. Then compare it with the port at `http://127.0.0.1:8001/`.
