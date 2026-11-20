# MAD Module — DevOps Practical සම්පූර්ණ Guide එක

මෙම folder එකේ lecturer දීපු **Phase 1–6** සඳහා අවශ්‍ය code files සියල්ල තියෙනවා. ZIP එක extract කර VS Code වල **File > Open Folder > devops-practical** යන්න. Terminal එක **PowerShell** ලෙස open කරන්න.

**වැදගත්:** දැනට root `app.py` එක අවසාන **V2** version එක. Practical එක පළමු පියවරේ සිට demonstrate කරන්න නම් පහත පරිදි `stages` folder එකේ V1 files copy කරගෙන පටන් ගන්න. එක් එක් phase එකේ `app.py` සහ `test_app.py` **දෙකම** වෙනස් කළ යුතුයි.

## Phase 1 — Flask Application + Test + Git

**1. මුලින් V1 files තෝරන්න** (practical එක මුල ඉඳන් කරනවා නම්):

```powershell
Copy-Item stages/phase1_app.py.txt app.py -Force
Copy-Item stages/phase1_test_app.py.txt test_app.py -Force
```

**2. Python packages install කරන්න:**

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m pytest -q
```

`1 passed` ලෙස result එක එන්න ඕන. Application එක run කරන්න:

```powershell
python app.py
```

Browser එකෙන් **http://localhost:5000** බලන්න. Output එක: **Hello, DevOps World! V1**. Stop කරන්න `Ctrl + C`.

**3. GitHub repo එකක් create කරන්න:** https://github.com/new වෙත ගිහින් `devops-practical` නමින් empty public repository එකක් හදන්න. Repo එකට GitHub හරහා README/License add නොකරන්න (local files වලින් push කරන නිසා).

```powershell
git init
git add .
git commit -m "Initial commit: App and tests"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/devops-practical.git
git push -u origin main
```

`YOUR_GITHUB_USERNAME` ඔබේ username එකට replace කරන්න. Git identity error ආවොත් `git config --global user.name "YOUR NAME"` හා `git config --global user.email "YOUR EMAIL"` set කරන්න. GitHub authentication සඳහා browser/credential manager එක භාවිතා කරන්න; password / tokens කාටවත් share කරන්න එපා.

**Note:** Worksheet එකේ create කරන repo එක `devops-practical` වුණත් sample remote URL එකේ `devops-masterclass` තියෙනවා. ඔබ ඇත්තටම හදපු repo එකේ නම යොදාගන්න.

## Phase 2 — GitHub Actions (CI)

`.github/workflows/ci.yml` file එක දැනටමත් සකස් කරලා තියෙන්නේ. `main` branch එකට push කරන විට unit test ස්වයංක්‍රීයව run වෙනවා.

GitHub Repo → **Actions** tab → **CI Pipeline** → green tick එක බලන්න. V1 files සහ test එක match වුණොත් pass විය යුතුයි. Workflow file එක මුල් commit එකේම ඇතුළත් වුණා නම් Phase 2 වෙනම commit එකක් නැතුවත් CI run වෙනවා.

අලුත් commit එකකින් demonstrate කරන්න අවශ්‍ය නම් workflow එක වෙනස් කළ පසු:

```powershell
git add .github/workflows/ci.yml
git commit -m "Update CI pipeline"
git push
```

## Phase 3 — Docker

පළමුව **Docker Desktop** install/open කරලා Docker engine running බව තහවුරු කරන්න. https://docs.docker.com/desktop/setup/install/windows-install/

```powershell
docker --version
docker build -t flask-app:v1 .
docker run --rm -p 5000:5000 flask-app:v1
```

http://localhost:5000 open කර V1 app එක පෙන්වනවාද බලන්න. `Ctrl+C` වලින් stop කරන්න.

Docker Hub login හා push:

```powershell
docker login
docker tag flask-app:v1 YOUR_DOCKERHUB_USERNAME/flask-app:v1
docker push YOUR_DOCKERHUB_USERNAME/flask-app:v1
```

`YOUR_DOCKERHUB_USERNAME` replace කරන්න. Docker Hub හි image එක **public** ලෙස accessible බව confirm කරන්න (Kubernetes pull සඳහා).

## Phase 4 — Kubernetes / Minikube

`kubectl` සහ `minikube` install කර Docker Desktop open කරගෙන:

```powershell
kubectl version --client
minikube version
minikube start --driver=docker
```

**අත්‍යවශ්‍ය:** `k8s/deployment.yaml` file එකේ `image: your-dockerhub-username/flask-app:v1` හි `your-dockerhub-username` තමන්ගේ Docker Hub username එකට වෙනස් කරන්න. `v1` image එක Docker Hub upload කරලා තිබිය යුතුයි.

Project root terminal එකෙන්:

```powershell
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl get deployments
kubectl get pods
minikube service flask-service
```

Pods **2/2 Running** වුණාද බලන්න. Browser එකේ app එක open වෙන්න ඕන.

## Phase 5 — Chaos / Self-Healing Test

```powershell
kubectl get pods
kubectl delete pod ACTUAL_POD_NAME
kubectl get pods -w
```

`ACTUAL_POD_NAME` වෙනුවට `kubectl get pods` output එකෙන් එක pod එකක නම දාන්න. Kubernetes අලුත් pod එකක් සාදා replicas **2** තබාගන්නවාද බලන්න. Watch එක නවත්වන්න `Ctrl+C`.

## Phase 6 — Render Deployment + V2 Auto Deploy

**1. මුල් Render version එකට app + test මාරු කරන්න:**

```powershell
Copy-Item stages/phase6_render_app.py.txt app.py -Force
Copy-Item stages/phase6_render_test_app.py.txt test_app.py -Force
python -m pytest -q
git add app.py test_app.py
git commit -m "Update app to use dynamic PORT for Render"
git push
```

**2. Render.com** → sign in with GitHub → **New > Web Service** → GitHub repository `devops-practical` connect කරන්න. Runtime **Docker**, Branch **main**, Name **my-flask-devops-app** (හෝ වෙනත් unique name එකක්), available **Free** plan තෝරන්න. Deploy කරන්න. Live වූ පසු Render දෙන සත්‍ය URL එක open කරන්න. Output: **Hello, DevOps World! We are LIVE on Render!**

*CI pass වුණාට පස්සේ deploy අවශ්‍ය නම්* Render → Service Settings → auto-deploy සඳහා **After CI Checks Pass** option එක ලැබුණොත් ඒක තෝරන්න. GitHub repo එක linked වීම වැදගත්.

**3. Final V2 change කර auto deployment demonstrate කරන්න:**

```powershell
Copy-Item stages/phase6_final_app.py.txt app.py -Force
Copy-Item stages/phase6_final_test_app.py.txt test_app.py -Force
python -m pytest -q
git add app.py test_app.py
git commit -m "Testing full automated deployment"
git push
```

GitHub Actions green tick → Render new deployment Live → තමන්ගේ Render URL refresh කරන්න. Final output: **CI/CD and Docker Pipeline is a massive success! V2**.

## දැනට ZIP එකේ state එක

- සියලුම source, workflow, Docker සහ Kubernetes configs **සූදානම්**.
- Root files `app.py` සහ `test_app.py` **V2** version එකෙන් තියෙන්නේ.
- මුල සිට practical එක කරන්න නම් Phase 1 commands එකෙන් V1 වලට යන්න.
- GitHub / Docker Hub / Render වෙත files publish කිරීම සහ Minikube cluster එක run කිරීම **ඔබේ accounts හා computer එකෙන්** කළ යුතුයි; ඒවා ZIP එකෙන් ස්වයංක්‍රීයව සිදු වී නැහැ.

Screenshots සඳහා `EVIDENCE_CHECKLIST.md` බලන්න.
