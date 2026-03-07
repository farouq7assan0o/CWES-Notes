# Authentication Flow (Text Flowchart)

```
START
  >
  User opens app
  >
  Has account?
  >-------------------- NO -------------------->
  |                                           |
  |  REGISTRATION FLOW                        |
  |                                           |
  |  Enter email                              |
  |    >                                     |
  |  Select secret file                      |
  |    >                                     |
  |  Enter PIN / passphrase                  |
  |    >                                     |
  |  OPAQUE registration                    |
  |    >                                     |
  |  Create passkey (WebAuthn)               |
  |    >                                     |
  |  Store public key on server              |
  |    >                                     |
  |  Mark device as TRUSTED                  |
  |    >                                     |
  |  Account created                         |
  |                                           |
  >-------------------- YES -------------------<
                      |
                      |
                      v
                LOGIN FLOW
                      |
                      v
              Enter email
                      >
              Select secret file
                      >
              Enter PIN / passphrase
                      >
              OPAQUE authentication
                      >
              Knowledge valid?
              >--------- NO ---------> LOGIN FAILED
              |
              YES
              |
              v
        Request WebAuthn assertion
                      >
              Trusted device available?
              >--------- NO ---------> SHOW RECOVERY
              |
              YES
              |
              v
         User verifies (FaceID / PIN)
                      >
         Device signs challenge
                      >
         Server verifies public key
                      >
              Assertion valid?
              >--------- NO ---------> LOGIN FAILED
              |
              YES
              |
              v
             LOGIN SUCCESS
                      >
             Create session
                      >
                    END
```

---

# Device Management Flow

```
LOGGED IN
  >
  User wants to add new device?
  >--------- NO ---------> CONTINUE SESSION
  |
  YES
  |
  v
Require existing trusted device
  >
Create new passkey on new device
  >
Store new public key on server
  >
Mark device as TRUSTED
  >
DONE
```

---

# Recovery Flow

```
LOGIN FAILED (No trusted device)
  >
  Has another trusted device?
  >--------- YES --------->
  |                        |
  |    Login from that     |
  |    device              |
  |      >                 |
  |    Add new device      |
  |      >                 |
  |    DONE                |
  |
  NO
  |
  v
Start recovery
  >
Prove knowledge (OPAQUE)
  >
Strong recovery required
  >
Recovery successful?
  >------ NO ------> ACCOUNT LOCKED / SUPPORT
  |
  YES
  |
  v
Register new passkey
  >
Mark as TRUSTED
  >
LOGIN SUCCESS
```

---

- `OPAQUE` → proves **knowledge**
    
- `WebAuthn` → proves **trusted device**
    
- No browser storage used
    
- Trusted device is mandatory for login