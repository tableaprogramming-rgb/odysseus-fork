---
title: Odysseus POC - Model Download Roadblock Report
date: 2026-06-06
status: BLOCKED
---

# Odysseus POC: Model Download Roadblock

## Summary
Attempting to download LFM2-8B-A1B model variants through Odysseus UI is consistently failing. Direct UI-based downloads are not viable due to repeated stalls, lock file contention, and network connectivity issues.

---

## Issues Encountered

### Issue 1: Repeated Stalls & Timeouts
- **Status**: LFM2-8B-A1B download stalled after ~10 minutes (June 5)
- **Duration**: Elapsed 122 minutes before stall detected
- **Root Cause**: Network instability or HuggingFace rate limiting
- **Result**: Download never completed, required restart

### Issue 2: Lock File Contention
- **Status**: LFM2-8B-A1B-GGUF download stuck in lock acquisition loop (June 6)
- **Lock Path**: `/Users/ericmagto/.cache/huggingface/hub/.locks/models--unsloth--LFM2-8B-A1B-GGUF/[hash].lock`
- **Wait Time**: 7+ minutes (431.2+ seconds elapsed) waiting for lock
- **Root Cause**: Possible stale lock file from previous crash or competing process
- **Resolution Attempted**: Cleared lock files and partial downloads

### Issue 3: Very Slow Internet Connection
- **Download Speed**: 0.23 Mbps (~29 KB/sec)
- **Expected Download Time**:
  - LFM2-8B-A1B (6GB): ~72 hours
  - LFM2-8B-A1B-GGUF (smaller): Still 10+ hours
- **Device**: Mac M2, 10.7GB RAM available
- **Connection Type**: WiFi (appears unstable)
- **Impact**: Even direct CLI downloads would be extremely slow

---

## What Was Attempted

✅ **Completed:**
1. Cloned Odysseus repo
2. Ran `./start-macos.sh` - all dependencies installed
3. Set HF_TOKEN in `.env` file
4. Odysseus application running at http://127.0.0.1:7860
5. Attempted LFM2-8B-A1B download (stalled)
6. Switched to LFM2-8B-A1B-GGUF variant (lock contention)
7. Cleared stale lock files and partial downloads

❌ **Failed:**
- Downloading via Odysseus UI
- Repeated restart attempts (same issues recur)
- Internet speed diagnostics showed 0.23 Mbps connection

---

## Root Causes

1. **Odysseus UI Download Mechanism**
   - May not support resume-on-disconnect properly
   - Lock file handling appears fragile
   - No clear error messages when failing

2. **Network Connectivity**
   - Connection speed too low (0.23 Mbps)
   - Possible WiFi instability
   - Multiple timeouts during transfer

3. **Model Size**
   - Original model (6GB) too large for slow connection
   - Even quantized variant (GGUF) still large

---

## Recommended Next Steps

### Short-term (When Returning to This Task)

1. **Improve Network Stability**
   - Switch to wired Ethernet connection (if possible)
   - Move closer to WiFi router
   - Restart router
   - Check for background network usage
   - Retest internet speed

2. **Use CLI Download Instead of UI**
   ```bash
   # Source venv first
   source /Users/ericmagto/Projects/personal/odysseus-poc/venv/bin/activate

   # Use huggingface-cli with resume
   huggingface-cli download LM-Sys/lfm2-8b-a1b-gguf \
     --local-dir ~/.cache/huggingface/hub \
     --resume-download
   ```

3. **Try Smaller Model First**
   - Download Phi-2 (2.7GB) or TinyLlama (0.6GB)
   - Verify download process works at all
   - Then scale up to larger models

### Medium-term

1. Check Odysseus GitHub issues for known download problems
2. Consider alternative model distribution methods:
   - Direct from HuggingFace with git-lfs
   - Alternative model sources (Ollama, etc.)
   - Local model conversion from GGUF format

3. Test Odysseus with pre-downloaded models (if possible)

### Long-term

- Evaluate if Odysseus is the right tool for slow-connection environments
- Consider alternative self-hosted AI platforms with better offline/resumable downloads
- Document network requirements for Odysseus deployment

---

## Current System State

**Project Location**: `/Users/ericmagto/Projects/personal/odysseus-poc/`

**Application Status**:
- ✅ Running at http://127.0.0.1:7860
- ✅ All services initialized
- ✅ Admin account active
- ⚠️ No model downloaded/loaded yet

**HuggingFace Cache**:
- Cleaned of lock files (June 6, 08:20 UTC)
- Partial downloads removed
- Ready for fresh download attempt

**Network**:
- Internet Speed: 0.23 Mbps (extremely slow)
- Connection Type: WiFi
- Status: Unreliable for large downloads

---

## Files to Check Later

- `/Users/ericmagto/Projects/personal/odysseus-poc/.env` (HF token configured)
- `/Users/ericmagto/Projects/personal/odysseus-poc/venv/bin/activate` (Python venv)
- `~/.cache/huggingface/hub/` (model cache location)

---

## Notes for Next Session

- Don't attempt UI download again until network is verified stable
- Try CLI method first when resuming
- Start with smallest model (TinyLlama) to validate process
- Document actual download times achieved for future reference
