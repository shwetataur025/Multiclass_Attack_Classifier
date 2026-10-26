# Multi-Class Network Attack Classification (CICIDS2017)

**Program:** IBM SkillsBuild Cyber Security 2026 (conducted by BharatCares)
**Author:** YourName

## Project description

Most intrusion detection models only answer "attack or normal?". A security team also needs to know **which kind of attack** it is, because the response is different: a DDoS needs traffic filtering, a brute-force attack needs account lockout, a port scan is reconnaissance, a web attack needs a web application firewall.

This project trains machine learning models to classify network flows into **BENIGN, DoS, DDoS, PortScan, BruteForce (FTP/SSH-Patator), WebAttack and Bot** using flow-level statistics from CICIDS2017. Very rare attacks (Infiltration, Heartbleed) are dropped automatically when they have too few samples.

Four models are compared: Logistic Regression, Decision Tree, Random Forest and Gradient Boosting. Because the classes are very unequal in size, the main metric is **macro F1** (every class counts equally), and results are shown **per class** with a confusion matrix and a list of the most confused attack types. Feature importance and a class-by-feature heatmap show what separates each attack.

## Dataset

**CICIDS2017** from the Canadian Institute for Cybersecurity (University of New Brunswick).

- Official page: https://www.unb.ca/cic/datasets/ids-2017.html
- Kaggle copy of the CSV files: https://www.kaggle.com/datasets/chethuhn/network-intrusion-dataset (please check the link still works)

The labelled CSV files are one per day (the "MachineLearningCVE" version):

| File | Contents |
|---|---|
| `Monday-WorkingHours.pcap_ISCX.csv` | Benign only |
| `Tuesday-WorkingHours.pcap_ISCX.csv` | FTP-Patator, SSH-Patator |
| `Wednesday-workingHours.pcap_ISCX.csv` | DoS (Hulk, GoldenEye, slowloris, Slowhttptest), Heartbleed |
| `Thursday-WorkingHours-Morning-WebAttacks.pcap_ISCX.csv` | Web attacks (brute force, XSS, SQL injection) |
| `Thursday-WorkingHours-Afternoon-Infilteration.pcap_ISCX.csv` | Infiltration |
| `Friday-WorkingHours-Morning.pcap_ISCX.csv` | Bot |
| `Friday-WorkingHours-Afternoon-PortScan.pcap_ISCX.csv` | Port scan |
| `Friday-WorkingHours-Afternoon-DDos.pcap_ISCX.csv` | DDoS |

The dataset is **not included** because of its size. Put the CSV files in a folder called `data` next to the notebook. The notebook uses whatever files it finds, and needs at least three different classes (for example Tuesday, Wednesday and the two Friday afternoon files).

## Technologies used

- Python 3.9+
- pandas, NumPy (data handling)
- scikit-learn (models and metrics)
- matplotlib, seaborn (charts)
- joblib (saving the model)
- Jupyter Notebook

## Setup and run instructions

1. Install Python 3.9 or newer.
2. (Optional) create a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate        # Windows: venv\Scripts\activate
   ```
3. Install the dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Put the dataset CSV files in a folder named `data` next to the notebook.
5. Start Jupyter and open the notebook:
   ```bash
   jupyter notebook YourName_Multiclass_Attack_Classifier.ipynb
   ```
6. Choose **Kernel > Restart & Run All**.

### Configuration options (section 2 of the notebook)

| Setting | Meaning |
|---|---|
| `DATA_DIR` | Folder with the CSV files (default `data`) |
| `MAX_PER_CLASS` | Maximum flows kept per class (default 50,000). Lower it, for example to 20,000, if the computer is slow or runs out of memory |
| `MIN_CLASS_SAMPLES` | Classes with fewer samples than this are dropped (default 100) |
| `CLASS_GROUPS` | How original labels are grouped into attack families |

## Output

Charts, `model_comparison.csv`, `per_class_report.csv` and the trained model `attack_classifier.joblib` are saved to an `outputs/` folder next to the notebook.

## Files

| File | Purpose |
|---|---|
| `YourName_Multiclass_Attack_Classifier.ipynb` | Complete project code |
| `requirements.txt` | Python dependencies |
| `YourName_ProjectReport.docx` | Full project report |
| `README.md` | This overview |

## Key information and limitations

- Cleaning (file by file): identifier columns, infinite/missing values and duplicates removed; labels grouped into attack families.
- Each class is capped at `MAX_PER_CLASS` flows, so the test set is **not** as imbalanced as real traffic. Precision on a real network would be lower.
- Split: 80% train / 20% test, stratified, `random_state=42`. Class weights are used to handle unequal class sizes.
- CICIDS2017 is lab-generated, and researchers have reported flow-construction and labelling problems in it. High scores do not guarantee the same results on live networks.
- Infiltration and Heartbleed have only a handful of samples and are excluded, so the model cannot detect them.
