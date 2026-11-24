# Kaggle GPU (free tier) — render-farm runbook

Load for any heavy GPU job (Blender/Cycles renders, ML training) run on Kaggle via the CLI.
Official reference: Kaggle ships an agent skill — github.com/Kaggle/kaggle-cli/tree/main/skills (install to ~/.claude/skills/kaggle-cli).

## Limits (verified by our own runs unless marked)
- GPU quota ~30 h/week, counted per session-hour (T4×2 = 1 h/h). `kaggle quota` shows used/remaining/refreshAt (resets Sat 00:00 UTC).
- **Max 2 GPU batch sessions at once, QUEUED ones count.** Push error: "Maximum batch GPU session count of 2 reached". This is what "stuck in queue for hours" usually is.
- QUEUED with free slots = no T4 available (peak hours, 1 h+ seen). Kaggle-side; no CLI cancel — `kaggle kernels delete <k> -y` removes a queued job.
- Job script (code_file) must be < 1 MB, else a bare 400 on SaveKernel. Don't embed big images; ship them in a dataset.
- Free GPU = NvidiaTeslaT4 (×2, 16 GB each). P100 retired (CLI warns, runs T4). L4/A100/H100 = competition/admin only.
- Max run ~12 h (unverified, community).

## Best practices
- Few big jobs: each job pays queue wait + setup (apt libs + Blender download ~1–2 min). Batch many tasks (rooms) per job.
- Use both T4s: one process per GPU via CUDA_VISIBLE_DEVICES=0/1, split the frame list; each subshell must `cd` itself (`cd W && a & b` leaves b elsewhere).
- Assets in a private dataset (`datasets create/version --dir-mode zip`), re-uploaded only on content-hash change; shrink textures (2K for 1080p). Code travels inside the job script.
- Datasets mount under /kaggle/input/datasets/<owner>/<slug>/...; find dirs by name but skip ones nested inside model folders (models ship their own textures/).
- `kaggle kernels push -p DIR -t SECONDS --accelerator NvidiaTeslaT4` — always cap with -t so a hang can't burn quota.
- Poll `kernels status`; debug with `kernels logs <k> --follow`; fetch selectively with `kernels output <k> --file-pattern REGEX`.
- Print `kaggle quota` before big pushes; plan finals against remaining hours.
- Wrap every CLI call in a timeout + retry (CLI can hang on flaky networks/DNS). Retry pushes only on the slot-limit message; stop on any other error.
- Cycles: adaptive sampling + OIDN; 48 spp previews, 64–128 spp finals; persistent scene per process. ~100 s/frame 1080p 128 spp per T4 (indoor scenes).
- Secrets only via Kaggle web UI Add-ons → Secrets (`kaggle_secrets`), never in scripts.
