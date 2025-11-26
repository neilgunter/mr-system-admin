# SSH Key Authentication Troubleshooting Guide

## Common Connection Issues and Solutions

### 1. "Too many authentication failures" Error
**Cause**: SSH client presenting multiple keys, triggering server's security lockout
**Solutions**:
```bash
# Clear agent cache
ssh-add -D

# Specify exact key
ssh -i ~/.ssh/specific_key user@host

# Force single identity in config
Host *
    IdentitiesOnly yes
```

### 2. Permission Errors
**Symptoms**: "Permission denied (publickey)" despite correct key
**Fix**:
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/private_key
restorecon -Rv ~/.ssh  # SELinux contexts
```

### 3. Host Key Verification Failure
**When**: Server fingerprint changes or first connection
**Resolution**:
```bash
# Remove old fingerprint
ssh-keygen -R hostname

# Or manually edit known_hosts
vi ~/.ssh/known_hosts
```

### 4. SSH Agent Issues
**When**: Keep getting prompted for passphrase
**Debug**:
```bash
# Check agent status
ssh-add -l

# Add key to agent
ssh-add ~/.ssh/your_key

# Start agent if not running
eval "$(ssh-agent -s)"
```

### 5. Network/Firewall Problems
**Diagnosis**:
```bash
# Check if port 22 is open
telnet host 22
nc -zv host 22

# Test with verbose output
ssh -v user@host
```

### 6. Server-Side Configuration Issues
**Check**:
- SSH daemon running: `systemctl status sshd`
- Public key in correct user's `authorized_keys`
- `PubkeyAuthentication yes` in `/etc/ssh/sshd_config`
- No `Match` blocks restricting access

### 7. Key Format Problems
**When**: "Invalid format" errors
**Convert**:
```bash
# Convert old OpenSSH format to new
ssh-keygen -p -f ~/.ssh/old_key -m pem
```

## Advanced Troubleshooting Flow

1. **Client-side check**: `ssh -v user@host` - shows exact failure point
2. **Server logs**: Check `/var/log/auth.log` or `/var/log/secure`
3. **Network verification**: Confirm port 22 reachable
4. **Permission audit**: Verify all file permissions and ownership
5. **Config validation**: Test with minimal SSH config

## Quick Reference Commands

```bash
# Test connection with specific key
ssh -i ~/.ssh/key -v user@host

# Check server configuration
ssh -G host

# Copy key with debug
ssh-copy-id -i ~/.ssh/key.pub -v user@host

# Force new key acceptance
ssh -o StrictHostKeyChecking=no user@host
```

Remember: Most SSH issues boil down to permissions, incorrect key selection, or server-side configuration. The verbose flag (`-v`) is your best friend for diagnosing exactly where the handshake fails.
