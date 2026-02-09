দুই বছর আগে SkyNets Bangladesh শুরু করেছিল মাত্র দুটো পপ নিয়ে - মিরপুর এবং উত্তরা। প্রায় ৮ হাজার কাস্টমার। তখন তারা Nautobot সেটআপ করেছিল। সেই পরিকল্পনা অনুযায়ী কাজ করেছে, সফল হয়েছে।

এখন ২০২৭ সাল। SkyNets Bangladesh এর গ্রোথ হয়েছে বিশাল। বর্তমানে নর্থ ঢাকায় তাদের আটটা পপ, প্রায় পঞ্চাশ হাজার কাস্টমার। আর সবচেয়ে বড় কথা - তারা এখনও সেই একই Nautobot সিস্টেম ব্যবহার করছে যেটা দুই বছর আগে সেটআপ করেছিল।

কিন্তু স্কেল বাড়ার সাথে সাথে নতুন চ্যালেঞ্জ এসেছে। এই চ্যাপ্টারে আমরা দেখব কীভাবে SkyNets সেই চ্যালেঞ্জগুলো সামলেছে, কী কী পরিবর্তন করেছে, আর কীভাবে Nautobot তাদের এই স্কেলিং জার্নিতে সাহায্য করেছে।

### বর্তমান পরিস্থিতি - ২০২৭ সালের গল্প

আজ থেকে দুই বছর আগে (২০২৫) যেখানে ছিল:
- ২টা পপ (মিরপুর, উত্তরা)
- ২০টা ডিভাইস
- ৮ হাজার কাস্টমার
- ৩ জন টেকনিক্যাল টিম

এখন ২০২৭ সালে কোথায় এসেছে:
- ৮টা পপ (নর্থ ঢাকা জুড়ে)
- ১৫০+ ডিভাইস
- ৫০ হাজার কাস্টমার
- ১৮ জন টেকনিক্যাল টিম

#### নতুন পপগুলো

মিরপুর আর উত্তরার পাশাপাশি নতুন ছয়টা পপ যুক্ত হয়েছে:

```
১. কল্যাণপুর পপ - সেকশন ৩ (~৫ হাজার কাস্টমার)
২. বনানী পপ - রোড ১১ (~৬ হাজার কাস্টমার)
৩. গুলশান পপ - গুলশান-২ (~৮ হাজার কাস্টমার)
৪. মোহাম্মদপুর পপ - টাউন হল রোড (~৭ হাজার কাস্টমার)
৫. ধানমন্ডি পপ - রোড ২৭ (~৯ হাজার কাস্টমার)
৬. বারিধারা পপ - বারিধারা DOHS (~৫ হাজার কাস্টমার)
```

প্রতিটা পপে:
- ১টা কোর রাউটার
- ২-৩টা ডিস্ট্রিবিউশন সুইচ
- ১৫-২৫টা এক্সেস সুইচ

#### নতুন আপস্ট্রিম কানেকশন

শুধু BTCL না, এখন আরো যুক্ত হয়েছে:
- **Summit Communications** - সেকেন্ডারি প্রোভাইডার
- **BDCom** - ব্যাকআপ কানেকশন
- **NovoTel** - কর্পোরেট ক্লায়েন্টদের জন্য ডেডিকেটেড
- **ইন্টার-পপ লিংক** - নিজেদের ফাইবার দিয়ে পপ-টু-পপ কানেকশন

### প্রথম চ্যালেঞ্জ: লোকেশন হায়ারার্কি রি-অর্গানাইজ করা

৮ হাজার কাস্টমারের সময় সব ফ্ল্যাট স্ট্রাকচারে রাখা যেত। কিন্তু ৫০ হাজারে সেটা কাজ করে না। দরকার হায়ারার্কিক্যাল স্ট্রাকচার।

#### আগের স্ট্রাকচার (২০২৫):

```
Dhaka North Zone
  ├── Mirpur Cluster
  │    └── Mirpur POP
  └── Uttara Cluster
       └── Uttara POP
```

#### নতুন স্ট্রাকচার (২০২৭):

```
SkyNets Bangladesh Network
  └── Dhaka North Zone
       ├── Mirpur Cluster
       │    ├── Mirpur POP
       │    └── Kalyanpur POP (নতুন)
       │
       ├── Uttara Cluster
       │    ├── Uttara POP
       │    └── Baridhara POP (নতুন)
       │
       ├── Gulshan Cluster (নতুন)
       │    ├── Gulshan POP
       │    └── Banani POP
       │
       └── Central Cluster (নতুন)
            ├── Dhanmondi POP
            └── Mohammadpur POP
```

এই হায়ারার্কি তৈরি করতে নতুন Cluster এবং POP locations যোগ করতে হয়েছে।

**Gulshan Cluster তৈরি:**

```
Organization → Locations → + Add

Name: Gulshan Cluster
Location Type: Cluster
Parent: Dhaka North Zone
Status: Active
Description: Gulshan and Banani area coverage - high-value residential and commercial zone
```

**Gulshan POP তৈরি:**

```
Name: Gulshan POP
Location Type: POP
Parent: Gulshan Cluster
Physical Address: House 45, Road 11, Block CEN(A), Gulshan-2, Dhaka-1212
Latitude: 23.7925
Longitude: 90.4078
Contact Name: Salman Ahmed
Contact Phone: +880 1811-987654
Time Zone: Asia/Dhaka
Description: Premium POP serving Gulshan-2 area. 8000 customers, mostly high-bandwidth corporate and residential.
Comments:
  - Established: March 2026
  - Building: 5-story commercial building, 3rd floor
  - Fiber Routes: Dual path to Mirpur and Uttara POPs
  - Backup Power: 100 KVA generator + 2 hours UPS
```

একইভাবে বাকি পপগুলো যোগ করা হয়েছে।

#### হায়ারার্কি এর সুবিধা

এখন রিপোর্টিং অনেক সহজ:

**প্রশ্ন:** "Gulshan Cluster এ মোট কয়টা ডিভাইস?"

**উত্তর:** Devices → Devices → Filters → Location: Gulshan Cluster → দেখাবে সব ডিভাইস (গুলশান পপ + বনানী পপ)

**প্রশ্ন:** "Central Cluster এ কতটা আইপি ব্যবহৃত হচ্ছে?"

**উত্তর:** IPAM → Prefixes → Filter by Location: Central Cluster → utilization দেখুন

### দ্বিতীয় চ্যালেঞ্জ: ডিভাইস নেমিং স্কেল করা

আগের নেমিং কনভেনশন ছিল:

```
[Type]-[Zone]-[Site]-[Role]-[Number]
উদাহরণ: R-DN-MIR-CORE-01
```

এটা ভালো কাজ করছিল যখন দুটো সাইট ছিল। কিন্তু এখন আটটা সাইট। তাই নেমিং আপডেট করা হয়েছে:

#### নতুন সাইট কোড:

```
MIR = Mirpur
UTT = Uttara
KAL = Kalyanpur
BAN = Banani
GUL = Gulshan
MOH = Mohammadpur
DHA = Dhanmondi
BAR = Baridhara
```

#### উদাহরণ:

```
R-DN-GUL-CORE-01 = Router, Dhaka North, Gulshan, Core, #1
SW-DN-DHA-DIST-02 = Switch, Dhaka North, Dhanmondi, Distribution, #2
SW-DN-BAR-ACC-18 = Switch, Dhaka North, Baridhara, Access, #18
```

#### পুরোনো ডিভাইস রিনেম করা

সমস্যা হলো পুরোনো ডিভাইসগুলো আগের নেমিং-এ আছে। সেগুলো রিনেম করতে হয়েছে ম্যানুয়ালি (UI থেকে) বা API দিয়ে bulk rename।

ছোট সংখ্যক ডিভাইস হলে UI থেকে এক এক করে এডিট করা যায়। কিন্তু ১৫০+ ডিভাইস হলে API/script ভালো।

### তৃতীয় চ্যালেঞ্জ: IP Address Management স্কেলিং

৮ হাজার কাস্টমারের সময় যে /22 পাবলিক ব্লক ছিল সেটা যথেষ্ট ছিল। কিন্তু ৫০ হাজারে সেটা শেষ হয়ে গেছে। নতুন আইপি ব্লক এসেছে।

#### পুরোনো আইপি স্ট্রাকচার (২০২৫):

```
103.125.40.0/22 (1024 IPs)
  ├─ 103.125.40.0/24 - Residential Mirpur
  ├─ 103.125.41.0/24 - Residential Uttara
  ├─ 103.125.42.0/25 - Corporate
  └─ 103.125.42.128/25 - Infrastructure
```

#### নতুন আইপি স্ট্রাকচার (২০২৭):

আরো তিনটা /22 ব্লক এলোকেট হয়েছে:

```
103.125.40.0/22 (Original - প্রায় ফুল)
103.125.44.0/22 (New Block 1)
103.125.48.0/22 (New Block 2)
103.125.52.0/22 (New Block 3)
```

এখন এগুলোকে সুন্দর করে অর্গানাইজ করা হয়েছে:

```
Parent: 103.125.40.0/20 (Aggregate - সব ব্লক মিলে)
  │
  ├─ 103.125.40.0/22 - Mirpur/Kalyanpur Cluster
  │    ├─ 103.125.40.0/24 - Mirpur Residential
  │    ├─ 103.125.41.0/24 - Kalyanpur Residential
  │    ├─ 103.125.42.0/24 - Corporate (Mirpur/Kalyanpur)
  │    └─ 103.125.43.0/24 - Infrastructure
  │
  ├─ 103.125.44.0/22 - Uttara/Baridhara Cluster
  │    ├─ 103.125.44.0/24 - Uttara Residential
  │    ├─ 103.125.45.0/24 - Baridhara Residential
  │    ├─ 103.125.46.0/24 - Corporate (Uttara/Baridhara)
  │    └─ 103.125.47.0/24 - Reserved
  │
  ├─ 103.125.48.0/22 - Gulshan/Banani Cluster
  │    ├─ 103.125.48.0/24 - Gulshan Residential
  │    ├─ 103.125.49.0/24 - Banani Residential
  │    ├─ 103.125.50.0/24 - Corporate (Premium zone)
  │    └─ 103.125.51.0/24 - Reserved
  │
  └─ 103.125.52.0/22 - Central Cluster + Future
       ├─ 103.125.52.0/24 - Dhanmondi Residential
       ├─ 103.125.53.0/24 - Mohammadpur Residential
       ├─ 103.125.54.0/24 - Corporate (Central)
       └─ 103.125.55.0/24 - Future Expansion
```

#### IP Utilization Monitoring

৫০ হাজার কাস্টমারে আইপি ইউটিলাইজেশন ট্র্যাক করা অত্যন্ত জরুরি।

**IPAM → Prefixes** লিস্টে গেলে দেখবেন:

```
103.125.40.0/24 - 92% utilized (236/256 IPs used) - ⚠️ Almost Full
103.125.44.0/24 - 68% utilized (174/256 IPs used) - ✓ Healthy
103.125.48.0/24 - 45% utilized (115/256 IPs used) - ✓ Good
```

যখন কোনো প্রিফিক্স ৮০% এর বেশি ইউটিলাইজড হয়, তখন alert তৈরি করুন। নতুন প্রিফিক্স রিকোয়েস্ট করুন।

#### Private IP Management

পাবলিক আইপি ছাড়াও প্রাইভেট আইপি স্ট্রাকচার বড় হয়েছে:

```
10.10.0.0/16 - SkyNets Internal Network
  │
  ├─ 10.10.1.0/24 - Loopbacks (all routers)
  │    ├─ 10.10.1.1/32 - R-DN-MIR-CORE-01
  │    ├─ 10.10.1.2/32 - R-DN-UTT-CORE-01
  │    ├─ 10.10.1.3/32 - R-DN-GUL-CORE-01
  │    └─ ... (সব কোর রাউটার)
  │
  ├─ 10.10.10.0/24 - Management - Mirpur POP
  ├─ 10.10.11.0/24 - Management - Uttara POP
  ├─ 10.10.12.0/24 - Management - Gulshan POP
  ├─ 10.10.13.0/24 - Management - Banani POP
  └─ ... (প্রতিটা পপের জন্য আলাদা /24)
```

### চতুর্থ চ্যালেঞ্জ: VLAN Scaling

আগে ভিল্যান স্ট্রাকচার ছিল সিম্পল:

```
VLAN 10: Management
VLAN 100: Residential
VLAN 200: Corporate
```

এখন প্রতিটা সাইটের জন্য আলাদা ভিল্যান:

```
VLAN 10-19: Management (site-specific)
  - VLAN 10: MGMT_MIRPUR
  - VLAN 11: MGMT_UTTARA
  - VLAN 12: MGMT_GULSHAN
  - VLAN 13: MGMT_BANANI
  ... ইত্যাদি

VLAN 100-199: Residential (site-specific)
  - VLAN 100: RES_MIRPUR
  - VLAN 101: RES_UTTARA
  - VLAN 102: RES_GULSHAN
  - VLAN 103: RES_BANANI
  ... ইত্যাদি

VLAN 200-299: Corporate (সব সাইট শেয়ারড)
  - VLAN 200: CORP_STANDARD
  - VLAN 201: CORP_PREMIUM
  - VLAN 202: CORP_ENTERPRISE
```

এই সব ভিল্যান Nautobot এ CSV দিয়ে বাল্ক ইমপোর্ট করা হয়েছে।

### পঞ্চম চ্যালেঞ্জ: Cable Management বাস্তবতা

৮ হাজার কাস্টমারের সময় সব ক্যাবল ডকুমেন্ট করা সম্ভব ছিল। কিন্তু ৫০ হাজারে এসে বোঝা গেল প্রতিটা প্যাচ কর্ড ট্র্যাক করা impractical।

#### Pragmatic Cable Management Policy

SkyNets একটা প্র্যাগম্যাটিক পলিসি বানিয়েছে:

**অবশ্যই ডকুমেন্ট করবেন:**
- আপলিংক ক্যাবল (প্রোভাইডার টু কোর)
- কোর টু ডিস্ট্রিবিউশন লিংক
- ইন্টার-পপ ফাইবার লিংক
- ক্রিটিক্যাল ব্যাকআপ লিংক
- যেকোনো ৫০ মিটারের বেশি দৈর্ঘ্যের ক্যাবল

**ডকুমেন্ট করতে হবে না:**
- এক্সেস সুইচ টু কাস্টমার প্যাচ কর্ড
- NOC এর ইন্টারনাল প্যাচিং
- টেম্পোরারি টেস্ট ক্যাবল
- ২ মিটারের কম patch cables

এই পলিসি ডকুমেন্ট করে রাখা আছে এবং সব টিম মেম্বার জানে।

### ষষ্ঠ চ্যালেঞ্জ: Multi-Team Collaboration

৮ হাজার কাস্টমারের সময় তিনজন টেকনিশিয়ান ছিল। এখন ১৮ জন। বিভিন্ন টিম:

**NOC Team (৬ জন):**
- ২৪/৭ মনিটরিং
- ইনসিডেন্ট রেসপন্স
- কাস্টমার সাপোর্ট

**Field Operations (৮ জন):**
- নতুন কানেকশন ইনস্টলেশন
- ফল্ট রিপেয়ার
- সাইট মেইনটেন্যান্স

**Network Engineering (৩ জন):**
- নেটওয়ার্ক ডিজাইন
- কনফিগারেশন ম্যানেজমেন্ট
- ক্যাপাসিটি প্ল্যানিং

**Management (১ জন):**
- ওভারভিউ এবং রিপোর্টিং

#### Permission Structure

এই টিমগুলোর জন্য আলাদা আলাদা পারমিশন (আগের চ্যাপ্টারে দেখেছি)।

#### Collaboration Workflow

একটা স্ট্যান্ডার্ড ওয়ার্কফ্লো:

**নতুন সাইট যুক্ত করার সময়:**

১. **Network Engineer** নতুন সাইট প্ল্যান করে Nautobot এ:
   - Location তৈরি করে
   - IP allocation করে
   - Device types verify করে

২. **Field Team** সাইট সেটআপ করে:
   - ডিভাইস ইনস্টল করে
   - Serial numbers নোট করে
   - Cable routing করে

৩. **Network Engineer** Nautobot আপডেট করে:
   - Devices এড করে
   - Cables ডকুমেন্ট করে
   - IP assign করে

৪. **NOC Team** যাচাই করে:
   - সব ডিভাইস Nautobot এ আছে কিনা
   - Monitoring সেটআপ হয়েছে কিনা
   - Connectivity test করে

### সপ্তম চ্যালেঞ্জ: Data Quality Control

১৫০ ডিভাইস মানে ১৫০ জায়গায় ভুল হওয়ার সম্ভাবনা। Data Quality নিশ্চিত করা জরুরি।

#### Weekly Data Audit

SkyNets প্রতি সপ্তাহে একটা ডেটা অডিট করে:

**সোমবার: Device Audit**
- সব Active ডিভাইসের Serial Number আছে কিনা
- সব ডিভাইসের Location সঠিক কিনা
- নেমিং কনভেনশন মানা হচ্ছে কিনা

**বুধবার: IP Audit**
- কোনো ডুপ্লিকেট IP নেই তো
- কোনো IP ভুল prefix এ নেই তো
- DNS names unique কিনা

**শুক্রবার: Cable Audit**
- Critical cables ডকুমেন্টেড কিনা
- Cable status সঠিক কিনা (Connected/Planned)

#### Automated Validation

প্রতি সোমবার সকালে একটা Python script চলে যা validate করে এবং রিপোর্ট ইমেইল করে:

```
Missing Serial Numbers: 3 devices
Duplicate IPs: 0
Non-standard Device Names: 2 devices
Orphaned IPs (no device assigned): 5 IPs
```

টিম লিডার এই রিপোর্ট দেখে action নেন।

### অষ্টম চ্যালেঞ্জ: Performance এবং Response Time

১৫০+ ডিভাইস, ৮টা সাইট, হাজার হাজার আইপি - Nautobot UI স্লো হয়ে যাচ্ছিল না তো?

#### Performance Optimization

SkyNets কিছু optimization করেছে:

**১. PostgreSQL Tuning:**

```
shared_buffers = 4GB (ডিফল্ট 128MB থেকে বাড়ানো)
effective_cache_size = 12GB
work_mem = 64MB
maintenance_work_mem = 1GB
```

**২. Pagination বাড়ানো:**

User Preferences এ:
```
Items per page: 100 (ডিফল্ট 25 থেকে)
```

**৩. Saved Filters ব্যবহার:**

বারবার একই complex query না চালিয়ে Saved Filters ব্যবহার।

**৪. Regular Database Maintenance:**

মাসে একবার:
```bash
sudo -u postgres vacuumdb --analyze nautobot
```

এই optimizations এর পরে response time ভালো আছে। ডিভাইস লিস্ট লোড হতে ২-৩ সেকেন্ড লাগে যা acceptable।

### ৬ মাসের রোডম্যাপ - ৮ হাজার থেকে ৫০ হাজারে

এই স্কেলিং একদিনে হয়নি। ছয় মাস লেগেছে। এই ছিল রোডম্যাপ:

#### মাস ১-২: Foundation Strengthening

**মাস ১:**
- Location hierarchy আপডেট
- নতুন Clusters তৈরি (Gulshan, Central)
- নেমিং কনভেনশন ফাইনালাইজ
- পুরোনো ডিভাইস রিনেম প্ল্যান

**মাস ২:**
- পুরোনো ডিভাইস রিনেম execution
- নতুন আইপি ব্লক পাওয়া
- IP addressing স্কিম ডিজাইন
- Permission structure review

#### মাস ৩-৪: New Sites Onboarding

**মাস ৩:**
- কল্যাণপুর পপ যুক্ত (প্রথম নতুন সাইট - ধীরে ধীরে)
- গুলশান পপ যুক্ত
- প্রতিটা সাইটে ফুল ডকুমেন্টেশন

**মাস ৪:**
- বনানী পপ যুক্ত
- বারিধারা পপ যুক্ত
- Team training (নতুন members দের)

#### মাস ৫: IP এবং VLAN Restructuring

- নতুন IP blocks যুক্ত
- VLAN স্ট্রাকচার রিডিজাইন
- Migration প্ল্যানিং এবং execution

#### মাস ৬: Final Sites এবং Optimization

- ধানমন্ডি পপ যুক্ত
- মোহাম্মদপুর পপ যুক্ত
- Performance tuning
- Automation scripts তৈরি
- Team training complete

### Key Metrics - আগে এবং পরে

কিছু সংখ্যা যা সফলতা প্রমাণ করে:

**২০২৫ (৮ হাজার কাস্টমার):**
- নতুন সাইট যুক্ত করতে সময়: ২ সপ্তাহ
- ডেটা এন্ট্রি ভুলের হার: ~১২%
- NOC query response time: ৫-৮ মিনিট
- ম্যানুয়াল কাজ: ৭৫%

**২০২৭ (৫০ হাজার কাস্টমার):**
- নতুন সাইট যুক্ত করতে সময়: ৩ দিন
- ডেটা এন্ট্রি ভুলের হার: ~৪%
- NOC query response time: ৩০ সেকেন্ড
- ম্যানুয়াল কাজ: ২৫%

### শিক্ষা এবং পরামর্শ

SkyNets এর CTO জাহাঙ্গীর সাহেবের কিছু পরামর্শ:

**১. একবারে সব করতে যাবেন না**

"আমরা ভুল করেছিলাম প্রথমে সব নতুন সাইট একসাথে করতে গিয়ে। পরে বুঝলাম একটা একটা করে করলে ভালো হয়। কল্যাণপুর পপ সম্পূর্ণ করে তারপর গুলশান - এভাবে করলে lesson শিখতে পেরেছি।"

**২. নেমিং কনভেনশন শুরুতেই ভবিষ্যৎ মাথায় রেখে করুন**

"আমাদের একবার নাম চেঞ্জ করতে হয়েছে। প্রথমবারই যদি স্কেলেবল কনভেনশন করতাম, এত ঝামেলা হতো না।"

**৩. Data quality এ কম্প্রোমাইজ করবেন না**

"একটা ভুল ডেটা এন্ট্রি পুরো নেটওয়ার্কে সমস্যা করতে পারে। Weekly audit করুন, automated validation রাখুন।"

**৪. টিমকে সাথে নিন**

"Nautobot শুধু একজনের জিনিস না। পুরো টিম যদি ইউজ না করে, তাহলে ডেটা আউটডেটেড হয়ে যায়। প্রপার ট্রেনিং দিন, documentation রাখুন।"

**৫. Performance monitoring করুন**

"স্কেল বাড়ার সাথে সাথে রেগুলার পারফরম্যান্স চেক করুন। Database tuning, caching - এগুলো গুরুত্বপূর্ণ।"

### চ্যাপ্টার সারাংশ

এই চ্যাপ্টারে আমরা দেখলাম:

**স্কেলিং চ্যালেঞ্জ:**
- লোকেশন হায়ারার্কি রি-অর্গানাইজ করা
- ডিভাইস নেমিং স্কেল করা
- IP এবং VLAN ম্যানেজমেন্ট
- Cable management বাস্তবতা
- Multi-team collaboration
- Data quality control
- Performance optimization

**সফলতার চাবি:**
- ধাপে ধাপে স্কেল করা (একবারে সব না)
- স্কেলেবল নেমিং কনভেনশন
- প্র্যাগম্যাটিক পলিসি (সব ট্র্যাক না করে গুরুত্বপূর্ণ জিনিস ট্র্যাক করা)
- Regular audit এবং validation
- Team training এবং collaboration
- Performance monitoring

SkyNets Bangladesh এখন ৫০ হাজার কাস্টমারে স্ট্যাবল। তাদের লক্ষ্য পরের দুই বছরে ১ লক্ষ কাস্টমার। সেজন্য তারা এখন অটোমেশনের দিকে যাচ্ছে। পরের চ্যাপ্টারে আমরা দেখব কীভাবে API এবং Python দিয়ে Nautobot কে অটোমেট করা যায়।

---
