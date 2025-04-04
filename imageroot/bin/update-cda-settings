#!/usr/bin/env python3

#
# Copyright (C) 2025 Nethesis S.r.l.
# SPDX-License-Identifier: GPL-3.0-or-later
#
import sys
import os
import subprocess

# does CDA is enabled in the UI (default is disabled)
cda_status = os.getenv("CDA_STATUS", "False") == "True" # make boolean

# CDA settings is disabled
if not cda_status:
    print("CDA is disabled, skipping CDA settings", file=sys.stderr)

    # Disable the CDA flag
    command = [
        "podman", "exec", "lemonldapng-app", "/usr/share/lemonldap-ng/bin/lemonldap-ng-cli",
        "-yes", "1", "set", "cda", "0"
        ]
    subprocess.run(command, stderr=subprocess.DEVNULL, check=True)

else:
    print("CDA is enabled, setting CDA settings", file=sys.stderr)
    
    # Enable the CDA flag
    print("Enabling CDA settings", file=sys.stderr)
    command = [
        "podman", "exec", "lemonldapng-app", "/usr/share/lemonldap-ng/bin/lemonldap-ng-cli",
        "-yes", "1", "set", "cda", "1"
        ]
    subprocess.run(command, stderr=subprocess.DEVNULL, check=True)
