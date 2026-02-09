আগের তেরোটা চ্যাপ্টারে আমরা Nautobot-এর ওয়েব ইন্টারফেস দিয়ে সব কাজ করেছি। সাইট তৈরি করেছি, ডিভাইস এড করেছি, আইপি ম্যানেজ করেছি - সবকিছু ম্যানুয়ালি। কিন্তু এখন সময় এসেছে পরের লেভেলে যাওয়ার। 

যখন আপনার নেটওয়ার্ক বড় হতে থাকে, তখন ম্যানুয়াল কাজ করা সম্ভব না। কল্পনা করুন - প্রতিদিন ২০০টা নতুন কাস্টমার যুক্ত হচ্ছে। প্রতিটার জন্য আইপি অ্যালোকেট করতে হবে, ডকুমেন্ট করতে হবে। UI থেকে একটা একটা করে করলে পুরো দিন চলে যাবে। এজন্যই দরকার অটোমেশন। আর অটোমেশনের চাবি হলো API।

API কী জিনিস? সহজ ভাষায় বলতে গেলে, API (Application Programming Interface) হলো একটা সিস্টেমের সাথে অন্য সিস্টেম বা প্রোগ্রাম কথা বলার মাধ্যম। আপনি যেমন Nautobot-এর ওয়েব ইন্টারফেসে ক্লিক করে কাজ করেন, API দিয়ে একই কাজ করা যায় কোড লিখে। ধরুন আপনি একটা Python স্ক্রিপ্ট লিখলেন যেটা Nautobot-কে বলল "Mirpur POP-এর সব ডিভাইসের লিস্ট দাও"। Nautobot তখন API-এর মাধ্যমে সেই তথ্য পাঠিয়ে দেবে। আমাদের আগের বই "বাংলাদেশি আইএসপি এবং নেটওয়ার্ক অটোমেশন" বইতে REST API সম্পর্কে বিস্তারিত আলোচনা আছে - HTTP methods, JSON, authentication এসব নিয়ে। এই বইতে আমরা সরাসরি PyNautobot লাইব্রেরি দিয়ে কাজ করব, কারণ এটা API ইউজ করাকে অনেক সহজ করে দেয়। আপনাকে REST API-এর সব টেকনিক্যাল ডিটেইলস জানতে হবে না - PyNautobot ব্যাকগ্রাউন্ডে সব সামলাবে। আগের বই "নেটওয়ার্ক এবং আইএসপি অটোমেশন" এ দুটো চ্যাপ্টার আছে এটা নিয়ে।

এই চ্যাপ্টারে আমরা দেখব কীভাবে Python দিয়ে Nautobot-এর সাথে কথা বলতে হয়, কীভাবে ডেটা পড়তে হয়, কীভাবে নতুন জিনিস যোগ করতে হয়। শুরু করি PyNautobot দিয়ে।

### PyNautobot - Python থেকে Nautobot

PyNautobot হলো একটা Python লাইব্রেরি যেটা Nautobot API-এর সাথে কাজ করা সহজ করে দেয়। এটা Nautobot টিম নিজেই তৈরি করেছে, তাই সবচেয়ে ভালো support পাবেন।

### Python Environment সেটআপ

প্রথমে আপনার কম্পিউটারে Python ইনস্টল থাকতে হবে। বাংলাদেশের বেশিরভাগ ISP-তে Windows কম্পিউটার ইউজ হয়, তাই Windows-এর জন্য দেখাচ্ছি।

**Windows-এ Python ইনস্টল:**

১. python.org থেকে Python ডাউনলোড করুন (৩.৯ বা তার উপরে)
২. ইনস্টল করার সময় "Add Python to PATH" চেকবক্স টিক দিন
৩. Command Prompt ওপেন করে চেক করুন:

```bash
python --version
```

দেখাবে: `Python 3.11.5` বা এরকম কিছু।

**Linux/Mac-এ:**

সাধারণত Python আগে থেকেই ইনস্টল থাকে। চেক করুন:

```bash
python3 --version
```

### PyNautobot ইনস্টল করা

Command Prompt বা Terminal-এ:

```bash
pip install pynautobot
```

অপেক্ষা করুন। কয়েক সেকেন্ডে ইনস্টল হয়ে যাবে। দেখাবে:

```
Successfully installed pynautobot-2.1.1
```

### API Token তৈরি করা

PyNautobot দিয়ে Nautobot-এ কানেক্ট করতে হলে একটা API Token লাগবে। এটা একধরনের পাসওয়ার্ড।

Nautobot-এ লগইন করুন। উপরের ডান কোণায় আপনার ইউজারনেমে ক্লিক → **Profile** → **API Tokens** ট্যাব।

**+ Add Token** বাটনে ক্লিক করুন।

```
Description: Python Scripts
Write Enabled: ✓ (চেক করুন - যাতে শুধু read না, write-ও করতে পারেন)
```

**Create** করুন। একটা টোকেন জেনারেট হবে:

```
0123456789abcdef0123456789abcdef01234567
```

এই টোকেন কপি করে নিরাপদ জায়গায় সেভ করুন। **এটা আর দেখানো হবে না**। যদি হারিয়ে যায়, নতুন টোকেন তৈরি করতে হবে।

⚠️ **গুরুত্বপূর্ণ:** এই টোকেন কাউকে দেবেন না। এটা দিয়ে আপনার Nautobot-এ সব কাজ করা যায়।

## প্রথম Python স্ক্রিপ্ট - হ্যালো Nautobot

এখন একটা সিম্পল স্ক্রিপ্ট লিখি যেটা Nautobot-এর সাথে কানেক্ট করবে।

একটা নতুন ফাইল তৈরি করুন: `hello_nautobot.py`

```python
from pynautobot import api

# Nautobot connection
nb = api(
    url="https://nautobot.skynet.bd",  # আপনার Nautobot URL
    token="0123456789abcdef0123456789abcdef01234567"  # আপনার টোকেন
)

# Test করুন connection কাজ করছে কিনা
print(f"Connected to Nautobot: {nb.version}")
```

ফাইল সেভ করুন। এখন চালান:

```bash
python hello_nautobot.py
```

আউটপুট:

```
Connected to Nautobot: 2.0.5
```

অভিনন্দন! আপনার প্রথম PyNautobot স্ক্রিপ্ট কাজ করছে। 

যদি error আসে "SSL verification failed", তাহলে:

```python
nb = api(
    url="https://nautobot.skynet.bd",
    token="your-token",
    verify=False  # Self-signed certificate-এর জন্য
)
```

তবে production-এ `verify=False` ব্যবহার করবেন না। Proper SSL certificate ইউজ করুন।

## ডেটা রিড করা - Devices লিস্ট

এখন আসল কাজ শুরু করি। সব ডিভাইসের লিস্ট বের করব।

`list_devices.py`:

```python
from pynautobot import api

nb = api(
    url="https://nautobot.skynet.bd",
    token="your-token-here"
)

# সব ডিভাইস নিয়ে আসুন
devices = nb.dcim.devices.all()

print(f"Total devices: {len(devices)}\n")

# প্রতিটা ডিভাইসের নাম প্রিন্ট করুন
for device in devices:
    print(f"- {device.name}")
```

চালান:

```bash
python list_devices.py
```

আউটপুট:

```
Total devices: 127

- R-DN-MIR-CORE-01
- R-DN-KAL-CORE-01
- SW-DN-MIR-DIST-01
- SW-DN-MIR-DIST-02
- SW-DN-MIR-ACC-01
...
```

### একটু বিস্তারিত তথ্য

শুধু নাম না, আরো তথ্য দেখি:

```python
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

devices = nb.dcim.devices.all()

for device in devices[:10]:  # প্রথম ১০টা দেখাই
    print(f"\nDevice: {device.name}")
    print(f"  Type: {device.device_type}")
    print(f"  Location: {device.location}")
    print(f"  Status: {device.status}")
    print(f"  Serial: {device.serial}")
```

আউটপুট:

```
Device: R-DN-MIR-CORE-01
  Type: MikroTik CCR2004-1G-12S+2XS
  Location: Mirpur POP
  Status: Active
  Serial: ABC1234MIR001

Device: SW-DN-MIR-DIST-01
  Type: TP-Link TL-SG3428
  Location: Mirpur POP
  Status: Active
  Serial: TPL1234DIST01
...
```

## ফিল্টার করে খুঁজে বের করা

সব ডিভাইস না, নির্দিষ্ট কিছু চাই। যেমন শুধু মিরপুর পপের ডিভাইস।

```python
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

# শুধু Mirpur POP-এর ডিভাইস
mirpur_devices = nb.dcim.devices.filter(location="Mirpur POP")

print(f"Devices in Mirpur POP: {len(mirpur_devices)}\n")

for device in mirpur_devices:
    print(f"- {device.name}")
```

অথবা শুধু Core Routers:

```python
# শুধু Core Router role-এর ডিভাইস
core_routers = nb.dcim.devices.filter(role="Core Router")

print(f"Total Core Routers: {len(core_routers)}\n")

for router in core_routers:
    print(f"- {router.name} at {router.location}")
```

আউটপুট:

```
Total Core Routers: 8

- R-DN-MIR-CORE-01 at Mirpur POP
- R-DN-KAL-CORE-01 at Kalyanpur POP
- R-DN-UTT-CORE-01 at Uttara POP
...
```

### একাধিক ফিল্টার একসাথে

```python
# Mirpur POP-এর Active Core Routers
devices = nb.dcim.devices.filter(
    location="Mirpur POP",
    role="Core Router",
    status="active"
)

for device in devices:
    print(device.name)
```

## IP Address রিপোর্ট

এখন একটা কাজের স্ক্রিপ্ট লিখি - আইপি ইউটিলাইজেশন রিপোর্ট।

`ip_utilization.py`:

```python
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

# সব প্রিফিক্স নিয়ে আসুন
prefixes = nb.ipam.prefixes.all()

print("IP Utilization Report")
print("=" * 60)

for prefix in prefixes:
    # শুধু /24 prefixes দেখাই (আপনার প্রয়োজন অনুযায়ী ফিল্টার করুন)
    if "/24" in str(prefix.prefix):
        utilization = prefix.utilization if hasattr(prefix, 'utilization') else 0
        
        print(f"\nPrefix: {prefix.prefix}")
        print(f"  Location: {prefix.location}")
        print(f"  Description: {prefix.description}")
        print(f"  Utilization: {utilization}%")
        
        # সতর্কতা দিন যদি ৮০% এর বেশি ইউজ হয়ে যায়
        if utilization > 80:
            print(f"  ⚠️  WARNING: High utilization!")
```

চালান:

```bash
python ip_utilization.py
```

আউটপুট:

```
IP Utilization Report
============================================================

Prefix: 103.125.40.0/24
  Location: Mirpur POP
  Description: Residential customers - Mirpur
  Utilization: 87%
  ⚠️  WARNING: High utilization!

Prefix: 103.125.44.0/24
  Location: Uttara POP
  Description: Residential customers - Uttara
  Utilization: 45%
...
```

এই রিপোর্ট দেখে বুঝতে পারবেন কোন প্রিফিক্সে নতুন আইপি দরকার।

## নতুন ডেটা যোগ করা

এতক্ষণ শুধু পড়লাম। এখন দেখি কীভাবে নতুন কিছু যোগ করতে হয়।

### নতুন ডিভাইস তৈরি

```python
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

# নতুন একটা এক্সেস সুইচ এড করব
new_device = nb.dcim.devices.create(
    name="SW-DN-MIR-ACC-11",
    device_type="TP-Link TL-SG1024D",  # এই device type আগে থেকে থাকতে হবে
    role="Access Switch",
    location="Mirpur POP",
    status="active",
    serial="TPLACC011NEW",
    comments="Added via Python script"
)

print(f"Device created: {new_device.name}")
print(f"Device ID: {new_device.id}")
```

চালালে:

```
Device created: SW-DN-MIR-ACC-11
Device ID: 1234
```

এখন Nautobot UI-তে গিয়ে দেখুন। নতুন ডিভাইস তৈরি হয়ে গেছে।

### IP Address যোগ করা

```python
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

# নতুন আইপি
new_ip = nb.ipam.ip_addresses.create(
    address="10.10.10.99/24",
    status="active",
    description="Test IP added via script"
)

print(f"IP created: {new_ip.address}")
```

### বাল্ক ক্রিয়েশন - একসাথে অনেকগুলো

ধরুন একটা CSV ফাইল আছে নতুন কাস্টমারদের আইপির জন্য:

`new_customers.csv`:
```csv
ip,customer_name
10.10.100.10,Customer A
10.10.100.11,Customer B
10.10.100.12,Customer C
```

এগুলো একসাথে এড করি:

```python
import csv
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

# CSV ফাইল পড়ুন
with open('new_customers.csv', 'r') as f:
    reader = csv.DictReader(f)
    
    for row in reader:
        ip_address = f"{row['ip']}/32"
        description = f"Customer: {row['customer_name']}"
        
        # IP তৈরি করুন
        try:
            new_ip = nb.ipam.ip_addresses.create(
                address=ip_address,
                status="active",
                description=description
            )
            print(f"✓ Created: {ip_address} - {row['customer_name']}")
        except Exception as e:
            print(f"✗ Failed: {ip_address} - {e}")
```

চালালে:

```
✓ Created: 10.10.100.10/32 - Customer A
✓ Created: 10.10.100.11/32 - Customer B
✓ Created: 10.10.100.12/32 - Customer C
```

এভাবে একবারে শত শত কাস্টমার যোগ করতে পারবেন।

## ডেটা আপডেট করা

বিদ্যমান ডেটা চেঞ্জ করতে হলে:

```python
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

# একটা ডিভাইস খুঁজে বের করুন
device = nb.dcim.devices.get(name="SW-DN-MIR-ACC-11")

# Status আপডেট করুন
device.status = "offline"
device.comments = "Device offline for maintenance - 2025-02-08"
device.save()

print(f"Updated: {device.name} status to {device.status}")
```

### বাল্ক আপডেট

সব মিরপুর পপের ডিভাইসে একটা ট্যাগ যোগ করব:

```python
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

# মিরপুর পপের সব ডিভাইস
mirpur_devices = nb.dcim.devices.filter(location="Mirpur POP")

# "production" ট্যাগ আগে থেকে থাকতে হবে
production_tag = nb.extras.tags.get(name="production")

for device in mirpur_devices:
    # যদি ট্যাগ না থাকে, যোগ করুন
    if production_tag not in device.tags:
        device.tags.append(production_tag)
        device.save()
        print(f"✓ Tagged: {device.name}")
```

## একটা কমপ্লিট উদাহরণ - ডেইলি রিপোর্ট

এখন একটা প্র্যাক্টিক্যাল স্ক্রিপ্ট লিখি যেটা প্রতিদিন সকালে চালাবেন।

`daily_report.py`:

```python
from pynautobot import api
from datetime import datetime

nb = api(url="https://nautobot.skynet.bd", token="your-token")

print("=" * 70)
print(f"SkyNet Bangladesh - Daily Network Report")
print(f"Generated: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
print("=" * 70)

# 1. Total Statistics
print("\n📊 OVERALL STATISTICS")
print("-" * 70)

total_devices = nb.dcim.devices.count()
active_devices = nb.dcim.devices.filter(status="active").count()
offline_devices = nb.dcim.devices.filter(status="offline").count()

print(f"Total Devices: {total_devices}")
print(f"  ├─ Active: {active_devices}")
print(f"  └─ Offline: {offline_devices}")

# 2. Per-Location Breakdown
print("\n🏢 LOCATION BREAKDOWN")
print("-" * 70)

locations = nb.dcim.locations.filter(location_type="POP")
for location in locations:
    device_count = nb.dcim.devices.filter(location=location.name).count()
    print(f"{location.name}: {device_count} devices")

# 3. IP Utilization Alerts
print("\n⚠️  IP UTILIZATION ALERTS")
print("-" * 70)

prefixes = nb.ipam.prefixes.all()
high_util = []

for prefix in prefixes:
    if hasattr(prefix, 'utilization') and prefix.utilization > 80:
        high_util.append({
            'prefix': str(prefix.prefix),
            'location': str(prefix.location),
            'utilization': prefix.utilization
        })

if high_util:
    for item in high_util:
        print(f"⚠️  {item['prefix']} at {item['location']}: {item['utilization']}%")
else:
    print("✓ All prefixes have healthy utilization")

# 4. Devices without Serial Numbers
print("\n🔍 DATA QUALITY ISSUES")
print("-" * 70)

no_serial = nb.dcim.devices.filter(status="active", serial="")
if no_serial:
    print(f"⚠️  {len(no_serial)} active devices missing serial numbers:")
    for device in no_serial[:5]:  # Show first 5
        print(f"   - {device.name}")
else:
    print("✓ All active devices have serial numbers")

print("\n" + "=" * 70)
print("Report completed successfully")
print("=" * 70)
```

চালান:

```bash
python daily_report.py
```

আউটপুট:

```
======================================================================
SkyNet Bangladesh - Daily Network Report
Generated: 2025-02-08 09:00:00
======================================================================

📊 OVERALL STATISTICS
----------------------------------------------------------------------
Total Devices: 127
  ├─ Active: 124
  └─ Offline: 3

🏢 LOCATION BREAKDOWN
----------------------------------------------------------------------
Mirpur POP: 23 devices
Kalyanpur POP: 15 devices
Uttara POP: 18 devices
Banani POP: 12 devices
Gulshan POP: 16 devices
Mohammadpur POP: 14 devices
Dhanmondi POP: 17 devices
Baridhara POP: 12 devices

⚠️  IP UTILIZATION ALERTS
----------------------------------------------------------------------
⚠️  103.125.40.0/24 at Mirpur POP: 87%
⚠️  103.125.48.0/24 at Gulshan POP: 82%

🔍 DATA QUALITY ISSUES
----------------------------------------------------------------------
⚠️  3 active devices missing serial numbers:
   - SW-DN-MIR-ACC-08
   - SW-DN-BAN-ACC-03
   - SW-DN-GUL-DIST-02

======================================================================
Report completed successfully
======================================================================
```

এই স্ক্রিপ্ট প্রতিদিন সকালে cron/Task Scheduler দিয়ে চালান। আউটপুট ইমেইলে পাঠান।

### Windows Task Scheduler দিয়ে অটোমেট

Windows-এ প্রতিদিন সকাল ৮টায় চালানোর জন্য:

১. Task Scheduler ওপেন করুন
২. "Create Basic Task" ক্লিক করুন
৩. Name: "Nautobot Daily Report"
৪. Trigger: Daily, 8:00 AM
৫. Action: Start a program
   - Program: `python`
   - Arguments: `C:\scripts\daily_report.py`
৬. Finish

### Linux Cron দিয়ে

```bash
# crontab -e

# প্রতিদিন সকাল ৮টায়
0 8 * * * /usr/bin/python3 /home/skynet/scripts/daily_report.py > /home/skynet/reports/daily_$(date +\%Y\%m\%d).txt
```

## Error Handling - ভুল সামলানো

স্ক্রিপ্ট লেখার সময় Error Handling করা জরুরি। না হলে একটা ছোট সমস্যায় পুরো স্ক্রিপ্ট থেমে যাবে।

```python
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

# নিরাপদ উপায়ে ডিভাইস খুঁজুন
try:
    device = nb.dcim.devices.get(name="SW-DN-MIR-ACC-99")
    print(f"Found: {device.name}")
except ValueError:
    print("Device not found")
except Exception as e:
    print(f"Error: {e}")

# বাল্ক অপারেশনে Error Handling
devices_to_create = [
    {"name": "SW-DN-MIR-ACC-12", "location": "Mirpur POP"},
    {"name": "SW-DN-UTT-ACC-15", "location": "Uttara POP"},
]

for dev_data in devices_to_create:
    try:
        new_device = nb.dcim.devices.create(
            name=dev_data["name"],
            device_type="TP-Link TL-SG1024D",
            role="Access Switch",
            location=dev_data["location"],
            status="active"
        )
        print(f"✓ Created: {new_device.name}")
    except Exception as e:
        print(f"✗ Failed to create {dev_data['name']}: {e}")
        continue  # পরেরটায় চলে যান, পুরো স্ক্রিপ্ট থামবে না
```

## পরবর্তী ধাপ - Ansible সাথে ইন্টিগ্রেশন

PyNautobot দিয়ে ডেটা রিড/রাইট করতে পারলেন। পরের লেভেল হলো Ansible-এর সাথে ইন্টিগ্রেট করা।

Ansible হলো একটা অটোমেশন টুল যা দিয়ে নেটওয়ার্ক ডিভাইস কনফিগার করা যায়। Nautobot-এর সাথে Ansible ইউজ করলে:

- Nautobot থেকে ডিভাইস লিস্ট নিয়ে আসবেন
- সেই ডিভাইসগুলোতে কনফিগ পুশ করবেন
- আবার কনফিগ ব্যাকআপ নিয়ে Nautobot-এ স্টোর করবেন

### একটা সিম্পল Ansible Inventory

`inventory.py`:

```python
#!/usr/bin/env python3
import json
from pynautobot import api

nb = api(url="https://nautobot.skynet.bd", token="your-token")

inventory = {
    "all": {
        "hosts": [],
        "children": {}
    },
    "_meta": {
        "hostvars": {}
    }
}

# সব Active রাউটার
routers = nb.dcim.devices.filter(role="Core Router", status="active")

for router in routers:
    hostname = router.name
    
    # Management IP খুঁজুন
    mgmt_ip = None
    interfaces = nb.dcim.interfaces.filter(device=router.name)
    for interface in interfaces:
        ips = nb.ipam.ip_addresses.filter(assigned_object_id=interface.id)
        if ips:
            mgmt_ip = str(ips[0].address).split('/')[0]
            break
    
    if mgmt_ip:
        inventory["all"]["hosts"].append(hostname)
        inventory["_meta"]["hostvars"][hostname] = {
            "ansible_host": mgmt_ip,
            "location": str(router.location),
            "device_type": str(router.device_type)
        }

print(json.dumps(inventory, indent=2))
```

এই স্ক্রিপ্ট Ansible dynamic inventory হিসেবে কাজ করবে। Ansible কমান্ডে:

```bash
ansible -i inventory.py all -m ping
```

এতে Nautobot থেকে সব ডিভাইস নিয়ে এসে ping করবে।

## Best Practices

কিছু টিপস যা মাথায় রাখবেন:

**১. Token সিকিউরিটি:**

হার্ডকোড করবেন না:
```python
# ❌ খারাপ
token = "abc123..."
```

Environment variable ইউজ করুন:
```python
# ✅ ভালো
import os
token = os.environ.get('NAUTOBOT_TOKEN')
```

অথবা config ফাইল:
```python
# config.ini
[nautobot]
url = https://nautobot.skynet.bd
token = your-token-here
```

```python
# স্ক্রিপ্টে
import configparser
config = configparser.ConfigParser()
config.read('config.ini')

nb = api(
    url=config['nautobot']['url'],
    token=config['nautobot']['token']
)
```

**২. Logging যোগ করুন:**

```python
import logging

logging.basicConfig(
    filename='nautobot_script.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

logging.info("Script started")

try:
    devices = nb.dcim.devices.all()
    logging.info(f"Retrieved {len(devices)} devices")
except Exception as e:
    logging.error(f"Failed to retrieve devices: {e}")
```

**৩. Rate Limiting সতর্কতা:**

একবারে হাজার হাজার রিকোয়েস্ট পাঠাবেন না। Nautobot সার্ভার স্লো হয়ে যাবে।

```python
import time

for device in large_list_of_devices:
    # কাজ করুন
    process_device(device)
    
    # একটু wait করুন
    time.sleep(0.1)  # ১০০ms delay
```

এখন আপনার হাতে Nautobot-কে অটোমেট করার টুল আছে। আর ম্যানুয়াল কাজ করতে হবে না। একবার স্ক্রিপ্ট লিখে ফেললে সেটা বারবার ইউজ করতে পারবেন।

পরের চ্যাপ্টারে আমরা দেখব কীভাবে ৫০ হাজার থেকে ১ লক্ষ, তারপর ১ মিলিয়ন কাস্টমারে স্কেল করবেন। কী কী প্রস্তুতি দরকার, কী কী চ্যালেঞ্জ আসবে, আর কীভাবে Nautobot সেই জার্নিতে সাহায্য করবে।