# tacacs


```
key = "************************"
accounting file = /var/log/tac.acct

acl = default   {
                permit = 10\.10\.10\.

}


group = admins {
        login = PAM

        acl = default
        
        # IOS XE
        service = exec {
                priv-lvl = 15
        }
        # IOS XR (from Tassos - I haven't tested yet)
        service = exec {
                optional task="#root-system"
        }
        
        # NXOS
        service = shell {
                priv-lvl = 15
                shell:roles = "\"network-admin\""
        }
        
        # IOS XE/XR and NXOS
        default service = permit

}


user = admin {
        login = PAM
        member = admins
}

```

