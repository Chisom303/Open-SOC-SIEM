# Publishing checklist (delete this file before release if you like)

## 0. Fill placeholders (find and replace across the repo)
- `0000-0000-0000-0000` → your ORCID
- `YOUR AFFILIATION OR 'Independent Researcher'` in `.zenodo.json`
- `zenodo.XXXXXXX` → after step 3 (Zenodo shows the DOI after the first release)

## 1. ORCID (5 min)
Register free at orcid.org and add the ORCID to GitHub and Zenodo profiles.

## 2. GitHub
- Create a public repo `Open-Clinic-SRA-SIEM-Demo` and push these files
- Settings → Pages → deploy from `main` / root, so `index.html` gets a live URL
- Add topics: `siem`, `prompt-injection`, `federated-learning`, `healthcare-security`, `mitre-atlas`
- Check that the "Cite this repository" button appears (from `CITATION.cff`)

## 3. Zenodo DOI
- Log in to zenodo.org with GitHub → Settings → GitHub → toggle this repo ON
- On GitHub, create a release with tag `v1.0.0`. Zenodo archives it and mints a DOI.
- Copy the **concept DOI** (all versions) into README, CITATION.cff, index.html (`citeText`) and taxonomy.md, then release `v1.0.1`

## 4. Paper
- Write it yourself from `paper/abstract_and_outline.md`; add at least one real evaluation (see that file)
- Submit to a peer-reviewed workshop or poster track
- arXiv: first-time submitters need a personal endorsement from an established cs author (or prior accepted authorship). Ask a co-author or mentor in security research.

## 5. Visibility
- Record a 60-second screen capture of the Prompt Injection and FL Poisoning scenarios; put it in the README and on LinkedIn
- Google Scholar profile: add the Zenodo record and the paper once it's indexed
- Offer the demo to security and health-informatics instructors. Course adoption is real, documentable use.
