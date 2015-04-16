winreg
======

Rust bindings to MS Windows Registry API. Work in progress.

## Usage

```rust
extern crate winreg;
use std::path::Path;
use winreg::RegKey;
use winreg::types::*;

fn main() {
    println!("Reading some system info...");
    let hklm = RegKey::predef(HKEY_LOCAL_MACHINE);
    let cur_ver = hklm.open_subkey("SOFTWARE\\Microsoft\\Windows\\CurrentVersion",
                                   KEY_READ).unwrap();
    let pf: String = cur_ver.get_value("ProgramFilesDir").unwrap();
    let dp: String = cur_ver.get_value("DevicePath").unwrap();
    println!("ProgramFiles = {}\nDevicePath = {}", pf, dp);

    println!("And now lets write something...");
    let hkcu = RegKey::predef(HKEY_CURRENT_USER);
    let path = Path::new("Software").join("WinregRsExample1");
    let key = hkcu.create_subkey(&path, KEY_ALL_ACCESS).unwrap();

    key.set_value("TestSZ", &"written by Rust").unwrap();
    let sz_val: String = key.get_value("TestSZ").unwrap();
    key.delete_value("TestSZ").unwrap();
    println!("TestSZ = {}", sz_val);

    key.set_value("TestDWORD", &1234567890u32).unwrap();
    let dword_val: u32 = key.get_value("TestDWORD").unwrap();
    println!("TestDWORD = {}", dword_val);

    key.create_subkey("sub\\key", KEY_ALL_ACCESS).unwrap();
    hkcu.delete_subkey_all(&path).unwrap();

    println!("Trying to open nonexisting key...");
    println!("{:?}", hkcu.open_subkey(&path, KEY_READ).unwrap_err());
}
```
