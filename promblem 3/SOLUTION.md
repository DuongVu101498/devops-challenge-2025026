# Troubleshooting High Memory Usage on Ubuntu 24.04 VM Running NGINX  

## Issue 1: NGINX Memory Leak or Misconfiguration  

### Expected Causes:  
- NGINX workers consuming excessive memory due to improper tuning.  
- High keepalive connections keeping memory usage elevated.  
- Memory leak from a faulty NGINX module.  

---

## Troubleshooting Steps  

### 1. Check Memory Usage per Process  
```bash
ps aux --sort=-%mem | head -10
```
If NGINX is consuming excessive memory, proceed with tuning.

### 2. Analyze NGINX Worker Processes
``` bash
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | grep nginx
```
If individual workers are consuming large amounts of memory, tuning may be required.

### 3. Check NGINX Configuration
```bash
cat /etc/nginx/nginx.conf
```
- Adjust worker_processes auto; to match CPU count.
- Adjust worker_connections to avoid excessive open connections.
- Tune keepalive_timeout to a reasonable value (e.g., keepalive_timeout 10;).
### 4. Test Configuration and Reload NGINX
```bash
nginx -t && systemctl reload nginx
```
### 5. Monitor for Memory Usage Improvement
```bash
free -m
vmstat 1 5
```
## Impact
- High memory usage can degrade system performance, causing slow responses or crashes.
- If memory exhaustion occurs, the kernel may start killing processes (OOM Killer).

## Recovery Steps
- Restart NGINX
```bash
systemctl restart nginx
```
- If a memory leak is identified, upgrade or downgrade NGINX as needed.
- If tuning does not help, consider allocating more memory to the VM.
  
## Issue 2: Memory Pressure from Cached Files and Logs  

### Expected Causes  
- Large log files consuming RAM due to disk caching.  
- Excessive file caching causing memory exhaustion.  

---

## Troubleshooting Steps  

### 1. Check Available Memory and Cached Usage  
```bash
free -m
```
- If most memory is used by "cached," excessive file caching might be the issue.
### 2. Check Large Log Files
```bash
du -sh /var/log/nginx/*
```
If log files are large, rotation might not be happening properly.
### 3. Manually Free Cache to Test Impact
```bash
sync; echo 3 | sudo tee /proc/sys/vm/drop_caches
free -m
```
If memory usage drops significantly, file caching was the issue.
### 4. Adjust Log Rotation Settings
```bash
cat /etc/logrotate.d/nginx
```
Ensure logs are rotated frequently to avoid excessive memory usage.
## Impact
- Large file caching can make memory unavailable for active processes.
- If the system runs out of memory, performance will degrade, and processes may crash.
## Recovery Steps:
- Adjust log rotation settings to avoid excessive log growth.
- Reduce disk cache pressure by setting vm.vfs_cache_pressure=100 in /etc/sysctl.conf.
- Restart the VM if memory is completely exhausted.

## Reference: Chat GPT