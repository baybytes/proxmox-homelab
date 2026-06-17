Issue encountered

iPhone clients could authenticate successfully to the SMB share but only had read access. Creating folders or uploading files failed, while macOS clients worked as expected.

Root cause

Missing Apple-specific Samba VFS modules (fruit, catia, streams_xattr).

Resolution
vfs objects = catia fruit streams_xattr
fruit:aapl = yes
fruit:metadata = stream
fruit:resource = stream
fruit:nfs_aces = no
ea support = yes

After restarting smbd, iOS clients gained full read/write functionality.
