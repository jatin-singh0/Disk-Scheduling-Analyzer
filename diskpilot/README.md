# DiskPilot – Disk Scheduling Analyzer (AOS Mini Project)

FCFS, SSTF, SCAN, C-SCAN, LOOK, C-LOOK — all algorithms are written in **C11** (`backend/algorithms.c`).
A small Python server loads the C code as a shared library and the browser UI (now a **dark theme: deep navy, violet and pink accents**)
visualises the result.

> Simulates how a disk head services cylinder requests under each algorithm and ranks them by total head movement. Also includes a terminal (CLI) version. **No pip/npm install needed.**

---

## How to run (step by step)

### 1. Install the requirements (one time)
| OS | Command |
|----|---------|
| Kali / Ubuntu / Debian | `sudo apt update && sudo apt install -y build-essential python3` |
| Fedora | `sudo dnf install -y gcc make python3` |
| macOS | `xcode-select --install` (Python 3: `brew install python`) |
| Windows | Use **WSL** (Ubuntu) and follow the Ubuntu command above |

Check everything is installed:
```bash
gcc --version && make --version && python3 --version
```

### 2. Unzip and enter the project
```bash
unzip diskpilot.zip
cd diskpilot
```

### 3. Start it
```bash
chmod +x run.sh
./run.sh
```
This single command will:
1. Build the C engine (`libdiskpilot.so` and the CLI)
2. Run the self-test (you should see `ALL TESTS PASSED (0 failures)`)
3. Start the web server and open **http://localhost:8000** in your browser

If the browser does not open automatically, open `http://localhost:8000` yourself.

### 4. Use the app
1. Click **Load example** (head 53, requests `98,183,37,122,14,124,65,67`) or type your own.
2. Click **Compare all algorithms** to rank all 6 algorithms, or **Run simulation** for the selected one.
3. Use **Play / Pause / Prev / Next** (or ← → and Space keys) to animate the head movement.
4. **Reports** section → *Download CSV* or *Export PDF (Print)*.

### 5. Stop it
Press **Ctrl + C** in the terminal.

---

## Other run options
```bash
./run.sh -p 8080                 # use a different port
./run.sh --host 0.0.0.0          # share on your LAN
./run.sh --no-browser            # don't auto-open the browser
./run.sh cli                     # interactive terminal menu
./run.sh cli --demo              # textbook example in the terminal
./run.sh cli -a scan -h 53 98 183 37 122 14 124 65 67
cd backend && make test          # run only the self-test
```

### Manual way (without run.sh)
```bash
cd backend && make && make test && cd ..
python3 server.py            # then open http://localhost:8000
```

## Expected output (sanity check)
Head 53, range 0–199, direction right:

| FCFS | SSTF | SCAN | C-SCAN | LOOK | C-LOOK |
|------|------|------|--------|------|--------|
| 640 | 236 | 331 | 382 | 299 | 322 |

## Project layout
```
diskpilot/
├── run.sh                  launcher (build + test + serve)
├── server.py               Python stdlib server, JSON API -> C library (ctypes)
├── backend/
│   ├── algorithms.c/.h     the 6 scheduling algorithms
│   ├── wasm_api.c          input validation + JSON output (used by server & WASM)
│   ├── main.c              CLI (menu + ASCII head-movement graph)
│   ├── test_algorithms.c   unit tests (textbook values)
│   └── Makefile            make / make test / make wasm (optional)
└── frontend/index.html     UI (canvas animation, comparison, CSV/PDF report)
```

## Changing the theme
All colours live in the `:root{...}` block at the top of `frontend/index.html`
(`--bg`, `--pink`, `--vio`, `--tx`, …). Canvas colours are the hex values inside the `draw()` function.

## Conventions
SCAN/C-SCAN travel to the disk edge; LOOK/C-LOOK stop at the last request. C-SCAN/C-LOOK return jumps
are counted in total movement. Duplicate requests are kept (0-cost seeks).

## Optional: WebAssembly mode
With Emscripten installed (`sudo apt install emscripten`): `cd backend && make wasm`, then serve `frontend/`
with any static server. The UI auto-falls back to WASM when the Python API is unavailable.

## Troubleshooting
| Problem | Fix |
|---------|-----|
| `Address already in use` | `./run.sh -p 8080` |
| `Permission denied: ./run.sh` | `chmod +x run.sh` |
| `gcc: command not found` | `sudo apt install build-essential` |
| UI shows "Engine offline" | You opened `index.html` directly — start with `./run.sh` and use the localhost URL |
| `libdiskpilot.so not found` | `cd backend && make` |
