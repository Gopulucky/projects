# Personal Family Bank Details Manager

A secure, offline-first application for managing family bank card details with local encryption and cross-platform deployment.

## 🔒 Security Features

- **100% Offline Operation**: No internet connection required
- **AES-256 Encryption**: All sensitive data encrypted at rest
- **Field-Level Encryption**: Card numbers, PINs, and CVV encrypted separately
- **Masked Display**: Sensitive data masked in UI (e.g., `**** **** **** 1234`)
- **Audit Logging**: Complete activity tracking
- **Platform-Specific Security**: Android secure storage, iOS app sandbox

## 🏗️ Architecture

### Core Components
- **Storage**: SQLite database with SQLCipher3 encryption
- **UI**: KivyMD for cross-platform interface
- **Encryption**: Fernet (AES-256) for field-level security
- **Platform Support**: Android, iOS, Windows, macOS, Linux

### Data Structure
```sql
-- Encrypted bank cards table
CREATE TABLE bank_cards (
    id INTEGER PRIMARY KEY,
    bank_name TEXT NOT NULL,
    ifsc_code TEXT NOT NULL,
    card_number_encrypted TEXT NOT NULL,
    pin_encrypted TEXT NOT NULL,
    validity_start TEXT NOT NULL,
    validity_end TEXT NOT NULL,
    cvv_encrypted TEXT NOT NULL,
    card_type TEXT NOT NULL,
    family_member TEXT NOT NULL,
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL
);

-- Family members with access levels
CREATE TABLE family_members (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    relationship TEXT NOT NULL,
    access_level TEXT NOT NULL
);

-- Audit log for security tracking
CREATE TABLE audit_log (
    id INTEGER PRIMARY KEY,
    action TEXT NOT NULL,
    card_id INTEGER,
    user TEXT NOT NULL,
    timestamp TEXT NOT NULL,
    details TEXT
);
```

## 🚀 Installation & Deployment

### Prerequisites
- Python 3.8+
- pip package manager
- For Android: Buildozer
- For iOS: Xcode (macOS only)

### 1. Local Development Setup

```bash
# Clone or download the project
cd secure-bank-manager

# Install dependencies
pip install -r requirements.txt

# Run the application
python secure_bank_manager.py
```

### 2. Android Deployment

```bash
# Install Buildozer
pip install buildozer

# Initialize buildozer (if not already done)
buildozer init

# Build APK
buildozer android debug

# The APK will be in bin/ directory
```

**Android Security Notes:**
- Database stored in app's private directory: `/data/data/com.family.bankmanager/files/`
- Full-disk encryption recommended
- Biometric authentication available

### 3. iOS Deployment

```bash
# Install kivy-ios
pip install kivy-ios

# Build for iOS
kivy-ios build secure_bank_manager

# Open in Xcode for final build
open secure_bank_manager-ios/secure_bank_manager.xcodeproj
```

**iOS Security Notes:**
- Database stored in app sandbox
- NSFileProtectionComplete enabled
- Keychain integration for master password

### 4. Desktop Deployment

```bash
# Windows
pyinstaller --onefile --windowed secure_bank_manager.py

# macOS
pyinstaller --onefile --windowed secure_bank_manager.py

# Linux
pyinstaller --onefile secure_bank_manager.py
```

## 🔐 Security Implementation

### Encryption Method
```python
# Master password to encryption key
def _generate_key(self, password):
    salt = b'secure_bank_salt_2024'
    kdf = PBKDF2HMAC(
        algorithm=hashes.SHA256(),
        length=32,
        salt=salt,
        iterations=100000,
    )
    key = base64.urlsafe_b64encode(kdf.derive(password.encode()))
    return key

# Field-level encryption
def encrypt_field(self, data):
    return self.cipher.encrypt(data.encode()).decode()

def decrypt_field(self, encrypted_data):
    return self.cipher.decrypt(encrypted_data.encode()).decode()
```

### Data Masking
```python
def mask_card_number(self, card_number):
    if len(card_number) >= 16:
        return f"{card_number[:4]} **** **** {card_number[-4:]}"
    return "**** **** **** ****"

def mask_cvv(self, cvv):
    return "***" if len(cvv) == 3 else "**"
```

## 📱 Usage Guide

### First Time Setup
1. Launch the application
2. Set a strong master password (minimum 8 characters)
3. Add family members with appropriate access levels
4. Start adding bank cards

### Adding Bank Cards
1. Click "+ Add Card" button
2. Fill in all required fields:
   - Bank Name
   - IFSC Code
   - Card Number
   - PIN (will be masked)
   - CVV (will be masked)
   - Family Member
3. Click "Save"

### Security Best Practices
- Use a strong master password
- Enable device encryption
- Regular backups to secure location
- Never share master password
- Log out when not in use

## 🛡️ Security Considerations

### PCI DSS Compliance
⚠️ **Important**: This application is for personal use only and is NOT PCI DSS compliant for commercial use.

**What's NOT stored:**
- CVV values (only masked display)
- PIN values (only masked display)
- Card numbers (encrypted at rest)

**What IS stored:**
- Encrypted card numbers
- Encrypted PINs
- Encrypted CVVs
- Bank names and IFSC codes
- Validity dates

### Risk Mitigation
| Risk | Mitigation |
|------|------------|
| Device theft | Full-disk encryption + app password |
| Screen snooping | Auto-lock + masked display |
| Data leakage | Never store plain text sensitive data |
| Unauthorized access | Master password protection |

## 🔧 Configuration

### Platform-Specific Settings
```python
# Android
android.permissions = INTERNET,WRITE_EXTERNAL_STORAGE,READ_EXTERNAL_STORAGE
android.api = 33
android.minapi = 21

# iOS
ios.app_name = SecureBankManager
ios.codesign.identity = iPhone Developer

# Desktop
Window.size = (400, 600)
```

### Customization
Edit `config.py` to modify:
- Database paths
- Encryption settings
- UI preferences
- Security timeouts

## 📊 Backup & Recovery

### Manual Backup
```bash
# Export encrypted database
cp secure_bank.db backup_$(date +%Y%m%d).db

# Export to CSV (encrypted)
python export_cards.py --password "your_master_password" --output backup.csv
```

### Automated Backup
```python
# Configure automatic backups
BACKUP_INTERVAL = 7  # days
BACKUP_RETENTION = 30  # days
BACKUP_LOCATION = "~/BankManager/Backups/"
```

## 🧪 Testing

### Unit Tests
```bash
# Run security tests
pytest tests/test_encryption.py

# Run UI tests
pytest tests/test_ui.py

# Run integration tests
pytest tests/test_database.py
```

### Security Testing
```bash
# Penetration testing
python security_test.py

# Encryption validation
python test_encryption.py
```

## 📋 Deployment Checklist

### Pre-Deployment
- [ ] Test on target platform
- [ ] Verify encryption works
- [ ] Check backup functionality
- [ ] Test password recovery
- [ ] Validate audit logging

### Android Specific
- [ ] Buildozer configuration correct
- [ ] Permissions properly set
- [ ] APK signed with strong keystore
- [ ] Test on multiple Android versions

### iOS Specific
- [ ] Xcode project configured
- [ ] Code signing certificates valid
- [ ] App sandbox enabled
- [ ] Keychain integration working

### Desktop Specific
- [ ] PyInstaller build successful
- [ ] Dependencies bundled correctly
- [ ] Cross-platform compatibility verified

## 🚨 Legal Disclaimer

This software is provided "as is" for personal use only. The developers are not responsible for:

- Data loss or corruption
- Security breaches
- Compliance with financial regulations
- Commercial use violations

**For commercial use, ensure compliance with:**
- PCI DSS standards
- Local financial regulations
- Data protection laws (GDPR, CCPA, etc.)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🆘 Support

For issues and questions:
1. Check the documentation
2. Review security guidelines
3. Test on clean environment
4. Report bugs with detailed information

---

**Remember**: This is a personal tool. Always follow your local laws and regulations regarding financial data storage and encryption. 