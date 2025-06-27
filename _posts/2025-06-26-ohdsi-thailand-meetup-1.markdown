---
layout: post
title:  "OHDSI Thailand Meetup #1: OMOP ETL Pipeline by Siriraj"
date:   2025-06-26 15:05:11 +0700
categories: meetup
---

- Recording: [YouTube](https://www.youtube.com/watch?v=4hujtIM6zJk)
- Slides: [PDF 2.1 MB]({{ site.baseurl }}/files/pdf/OHDSI-Thailand-Meetup-1-20250626.pdf)
- Links
  - SiData+'s [SQLMesh Poster at OHDSI APAC Symposium 2024](https://www.ohdsi.org/wp-content/uploads/2024/08/SiData-Poster-SQLMesh-APAC2024.pdf), [dbt Poster at OHDSI Global Symposium 2022](https://www.ohdsi.org/wp-content/uploads/2022/10/2-Pitchayarat-OHDSI2022Poster-Adulyanukosol-scaled.jpg) 
  - Demo ETL Pipeline on GitHub <https://github.com/sidataplus/demo-etl-sqlmesh-omop-synthea/>
  - Demo ETL Pipeline Presentation at OHDSI APAC meeting <https://www.youtube.com/watch?v=J0_2hkXz-HY>
  - SQLMesh <https://sqlmesh.com/>
  - SQLMesh Documentation <https://sqlmesh.readthedocs.io/en/stable/>
  - SQLMesh Quick Start Guide <https://sqlmesh.readthedocs.io/en/stable/quick_start/>
  - OHDSI Global Symposiums: [2025](https://www.ohdsi.org/ohdsi2025/), [2024](https://www.ohdsi.org/apac/), [2023](https://www.ohdsi.org/2023-collaborator-showcase/), [2022](https://www.ohdsi.org/2022-collaborator-showcase/)
  - OHDSI APAC: <https://www.ohdsi.org/apac/>

![Banner]({{ site.baseurl }}/files/img/OHDSI-Thailand-Meetup-1.jpg)

---

> สรุปเนื้อหาจาก Meetup โดย Google Gemini (อาจมีข้อผิดพลาด)

---

## จากข้อมูลดิบสู่ข้อมูลพร้อมใช้สำหรับงานวิจัย: เส้นทางของศิริราชกับ OMOP และ SQLMesh

โลกของการดูแลสุขภาพขับเคลื่อนด้วยข้อมูล  การมาโรงพยาบาลของผู้ป่วยแต่ละครั้ง การวินิจฉัย และการสั่งยาทุกรายการล้วนสร้างข้อมูลจำนวนมหาศาล ที่โรงพยาบาลศิริราช ซึ่งเป็นโรงพยาบาลที่ใหญ่ที่สุดในประเทศไทย มีข้อมูลเวชระเบียนอิเล็กทรอนิกส์ (EHR) ที่เก็บรวบรวมมากว่าสองทศวรรษ  ครอบคลุมผู้ป่วย 2.5 ล้านคน  และรายการข้อมูลเฉพาะแต่ละรายการอีกหลายร้อยล้านรายการ เช่น การวินิจฉัยและผลตรวจทางห้องปฏิบัติการ  แต่ข้อมูลดิบเหล่านี้ในรูปแบบดั้งเดิมมักมีความซับซ้อนและใช้งานเพื่องานวิจัยได้ยาก เนื่องจากความกังวลด้านความเป็นส่วนตัว ความไม่เป็นระเบียบ และรูปแบบที่ไม่สอดคล้องกัน

ความท้าทายนี้เป็นหัวข้อหลักของงาน OHDSI Thailand Meetup ครั้งแรกสุด ซึ่งจัดขึ้นเมื่อวันที่ 26 มิถุนายน 2568  โดยศูนย์นวัตกรรมข้อมูลศิริราช (SiData+)  ในเซสชันนี้ได้นำเสนอรายละเอียดแนวทางของโรงพยาบาลศิริราชในการแปลงคลังข้อมูลขนาดใหญ่ให้เป็นรูปแบบมาตรฐานที่พร้อมใช้งานสำหรับงานวิจัยโดยใช้ OMOP Common Data Model  พร้อมทั้งชูประเด็นการนำเครื่องมือใหม่ที่ทรงพลังอย่าง SQLMesh มาใช้งาน

### ความท้าทาย: ทำไมต้องสร้างมาตรฐานข้อมูลสุขภาพ?

การทำงานกับข้อมูล EHR ดิบนั้นมีอุปสรรคสำคัญหลายประการ:

- **การเตรียมข้อมูลที่ใช้เวลานาน:** นักวิจัยอาจต้องใช้เวลามากมายในการเตรียมข้อมูล กำหนดเกณฑ์การคัดเข้า/คัดออก และจัดรูปแบบข้อมูลเพื่อการวิเคราะห์
- **การขาดความสามารถในการทำซ้ำ:** สคริปต์ข้อมูลและแดชบอร์ดที่สร้างขึ้นเองมักมีความเฉพาะเจาะจงกับโครงสร้างข้อมูลของโรงพยาบาลแห่งเดียว ทำให้เป็นเรื่องยากสำหรับผู้อื่นที่จะนำไปใช้หรือทำการวิจัยซ้ำได้
- **ความร่วมมือที่ยากลำบาก:** การทำวิจัยแบบเครือข่ายระหว่างหลายสถาบันเป็นเรื่องท้าทายเมื่อข้อมูลของแต่ละแห่งอยู่ในรูปแบบที่แตกต่างกัน

### ทางออก: OHDSI และ OMOP Common Data Model

เพื่อแก้ไขปัญหาเหล่านี้ ชุมชนระดับโลกที่ชื่อว่า OHDSI (Observational Health Data Sciences and Informatics) ได้สร้างมาตรฐานแบบเปิดและไม่เสียค่าใช้จ่ายขึ้นมา นั่นคือ OMOP Common Data Model (CDM)  ด้วยการแปลงข้อมูลดั้งเดิมให้อยู่ในรูปแบบ OMOP CDM สถาบันต่าง ๆ จะสามารถสร้างโครงสร้างข้อมูลที่สอดคล้องกันได้  ซึ่งช่วยให้:

- **การวิจัยที่ทำซ้ำได้:** นักวิเคราะห์สามารถใช้เครื่องมือและชุดโค้ดที่มีอยู่สาธารณะเพื่อทำการศึกษาได้
- **การวิเคราะห์ที่ดียิ่งขึ้น:** ข้อมูลที่เป็นมาตรฐานช่วยให้การสร้างกลุ่มประชากร (cohorts) การประเมินความเป็นไปได้ของโครงการวิจัย และการแบ่งปันสคริปต์การวิเคราะห์ทำได้ง่ายขึ้น
- **ความร่วมมือที่ง่ายขึ้น:** สถาบันที่มีข้อมูลในรูปแบบ OMOP สามารถเข้าร่วมในโครงการวิจัยแบบเครือข่ายขนาดใหญ่ได้ง่ายขึ้น

### การสร้างไปป์ไลน์: กระบวนการ ETL

กระบวนการแปลงข้อมูลดิบไปเป็น OMOP CDM เรียกว่า ETL ซึ่งย่อมาจาก Extract (สกัด), Transform (แปลง), และ Load (นำเข้า)

1. **Extract:** การดึงข้อมูลจากระบบต้นทาง (เช่น ฐานข้อมูลผู้ป่วย ผลตรวจทางห้องปฏิบัติการ และยา)
2. **Transform:** การแปลงและจับคู่ข้อมูลต้นทางให้อยู่ในรูปแบบตารางและชุดคำศัพท์มาตรฐานของ OMOP CDM
3. **Load:** การนำข้อมูลที่มีโครงสร้างใหม่แล้วเข้าไปยังฐานข้อมูลปลายทางเพื่อนำไปใช้ในการวิเคราะห์ต่อไป

ที่ศิริราช ไปป์ไลน์นี้เกี่ยวข้องกับการดึงข้อมูลจากระบบต่าง ๆ ทุกคืนเข้ามาไว้ในคลังข้อมูล MS SQL Server  จากนั้น จะมีการใช้เครื่องมือสำหรับการแปลงข้อมูลโดยเฉพาะเพื่อปรับปรุงโครงสร้างข้อมูลให้อยู่ในรูปแบบ OMOP

### การเลือกเครื่องมือที่ใช่: เส้นทางของศิริราชจาก dbt สู่ SQLMesh

แม้ว่า OHDSI จะมีเครื่องมือบางอย่างให้ แต่การพัฒนา ETL หลักนั้นเป็นหน้าที่ของแต่ละสถาบัน เนื่องจากความซับซ้อนทางเทคนิคและความแตกต่างของประเภทฐานข้อมูล ความปลอดภัย และตรรกะทางธุรกิจ

ทีมข้อมูลของศิริราชได้ประเมินทางเลือกหลายอย่างสำหรับกระบวนการแปลงข้อมูล:

- **สคริปต์ที่เขียนขึ้นเอง (SQL, Python):** มีความยืดหยุ่นสูง แต่ดูแลรักษาและขยายระบบได้ยาก
- **เครื่องมือ ETL เชิงพาณิชย์ (เช่น MS SSIS):** อาจมีค่าใช้จ่ายสูงและมักพึ่งพาการใช้งานผ่านหน้าจอ (GUI) ซึ่งจัดการได้ยากสำหรับโครงการที่ซับซ้อนและมีโค้ดจำนวนมาก
- **dbt (data build tool):** เป็นเครื่องมือโอเพนซอร์สยอดนิยมที่ศิริราชเคยใช้ในช่วงแรก  อย่างไรก็ตาม ทีมงานพบข้อจำกัด โดยระบุว่า dbt ทำหน้าที่เหมือนเป็นเครื่องมือสร้างเทมเพลต (templating engine) และไม่ได้เข้าใจโค้ด SQL อย่างแท้จริง ซึ่งทำให้การหาข้อผิดพลาด (debug) ทำได้ยาก
- **SQLMesh:** เป็นเครื่องมือโอเพนซอร์สที่ใหม่กว่าซึ่งออกแบบมาเพื่อเอาชนะข้อจำกัดของ dbt  ศิริราชได้เปลี่ยนมาใช้ SQLMesh เนื่องจากเป็นโซลูชันที่มีประสิทธิภาพและเชื่อถือได้มากกว่าสำหรับการจัดการการแปลงข้อมูล OMOP ที่ซับซ้อน

### เจาะลึก SQLMesh: เหตุใดจึงเป็นเครื่องมือที่พลิกเกม

SQLMesh ได้รับเลือกเพราะถูกสร้างขึ้นมาเพื่อการสร้างแบบจำลองข้อมูลโดยเฉพาะ และมีคุณสมบัติขั้นสูงหลายอย่างที่เครื่องมืออื่นไม่มี:

- **ความเข้าใจในความหมายของ SQL (SQL Semantic Understanding):** SQLMesh ไม่ได้ทำงานแค่กับข้อความเหมือนเครื่องมืออื่น แต่สามารถวิเคราะห์และเข้าใจโค้ด SQL ได้จริง  ซึ่งช่วยให้สามารถตรวจจับข้อผิดพลาด (bugs) ได้ *ก่อน* ที่คำสั่งจะถูกรันบนฐานข้อมูล และยังสามารถสร้าง Column Lineage ที่ติดตามการไหลและการแปลงของข้อมูลได้อย่างละเอียด
- **สภาพแวดล้อมข้อมูลเสมือน (Virtual Data Environments):** ช่วยให้นักพัฒนาสามารถทำงานในสภาพแวดล้อมสำหรับการพัฒนา (development) ที่แยกออกมาได้อย่างปลอดภัย โดยไม่รบกวนข้อมูลที่ใช้งานจริง (production) แม้ว่าจะอยู่บนฐานข้อมูลเดียวกันก็ตาม
- **สัญญาข้อมูลและการตรวจสอบ (Data Contracts & Audits):** SQLMesh สามารถบังคับใช้กฎเกณฑ์คุณภาพข้อมูล (audits) เพื่อให้แน่ใจว่าการแปลงข้อมูลนั้นสมบูรณ์และถูกต้องก่อนที่จะนำไปใช้งานจริง  หากการแปลงข้อมูลไม่สมบูรณ์ ระบบจะป้องกันไม่ให้ข้อมูลที่ผิดพลาดเข้าสู่ระบบ production
- **การแปลภาษา SQL (SQL Transpilation):** สามารถแปลไวยากรณ์ของ SQL ระหว่างระบบฐานข้อมูลที่แตกต่างกันได้โดยอัตโนมัติ (เช่น จาก Microsoft SQL Server ไปเป็น DuckDB) ซึ่งมีประโยชน์อย่างมากในการย้ายระบบและการทำงานร่วมกัน
- **โมเดลแบบเพิ่มส่วน (Incremental Models):** สำหรับชุดข้อมูลขนาดใหญ่ SQLMesh สามารถตั้งค่าให้ประมวลผลเฉพาะข้อมูลใหม่หรือข้อมูลที่เปลี่ยนแปลงเท่านั้น ซึ่งช่วยลดระยะเวลาการทำงานจากหลายชั่วโมงเหลือเพียงไม่กี่นาที

### เริ่มต้นใช้งานและเข้าร่วมชุมชน

สำหรับผู้ที่ต้องการเริ่มต้นเส้นทาง OMOP ETL ของตนเอง ผู้บรรยายได้เสนอแนวทางที่ชัดเจนไว้ดังนี้:

1. **เริ่มต้นจากเอกสาร:** ศึกษาเอกสารและคู่มือ "Get Started" ของ SQLMesh
2. **ลองใช้เดโม:** โรงพยาบาลศิริราชได้เปิดให้ใช้ GitHub repository สาธารณะซึ่งมีการสาธิตการแปลงข้อมูลสังเคราะห์ไปเป็น OMOP โดยใช้ SQLMesh และ DuckDB
3. **สร้างทีละส่วน:** เริ่มจากการแปลงตารางที่เรียบง่ายแต่จำเป็นก่อน เช่น `person` และ `concept` ก่อนที่จะไปยังตารางที่ซับซ้อนมากขึ้น

งาน meetup สิ้นสุดลงด้วยการเชิญชวนให้ร่วมกันจัดตั้ง **OHDSI Thailand Chapter** อย่างเป็นทางการ  ซึ่งจะช่วยสร้างเครือข่ายที่เป็นทางการสำหรับแบ่งปันความรู้ สนับสนุนงานวิจัย และเพิ่มการมองเห็นของวงการวิทยาศาสตร์ข้อมูลของไทยในเวทีโลก  meetup ครั้งถัดไปมีกำหนดจัดขึ้นใน **วันพฤหัสบดีที่ 24 กรกฎาคม** ในหัวข้อ "ภาพรวมของ OHDSI" (Overview of OHDSI)

---

> Meetup Summary by Google Gemini (may contain errors)

---

## From Raw Data to Research-Ready: Siriraj's Journey with OMOP and SQLMesh

The world of healthcare runs on data. Every patient visit, diagnosis, and prescription generates a wealth of information. At Siriraj Hospital, Thailand's largest hospital, this amounts to over two decades of electronic health records (EHR), covering 2.5 million patients and hundreds of millions of individual events like diagnoses and lab results. But this raw data, in its native state, is often complex and difficult to use for research due to privacy concerns, messiness, and inconsistent formatting.

This challenge was the focus of the first-ever OHDSI Thailand Meetup on June 26, 2025, hosted by the Siriraj Informatics and Data Innovation Center (SiData+). The session detailed Siriraj's approach to transforming their vast data warehouse into a standardized, research-ready format using the OMOP Common Data Model, highlighting their adoption of a powerful new tool: SQLMesh.

### The Challenge: Why Standardize Health Data?

Working with raw EHR data presents significant hurdles:

- **Time-Consuming Preparation:** Researchers can spend an inordinate amount of time preparing data, defining inclusion/exclusion criteria, and formatting it for analysis.
- **Lack of Reproducibility:** Custom data scripts and dashboards are often specific to one hospital's data structure, making it difficult for others to use or reproduce the research.
- **Difficult Collaboration:** Conducting network research studies across multiple institutions is challenging when everyone's data is in a different format.

### The Solution: OHDSI and the OMOP Common Data Model

To address these issues, a global community called OHDSI (Observational Health Data Sciences and Informatics) has established a free and open standard: the OMOP Common Data Model (CDM). By transforming their native data into the OMOP CDM, institutions create a consistent structure. This allows for:

- **Reproducible Research:** Analysts can use publicly available tools and code packages to run studies.
- **Enhanced Analytics:** Standardized data makes it easier to generate cohorts, assess study feasibility, and share analytics scripts.
- **Easier Collaboration:** Institutions with OMOP-formatted data can more easily participate in large-scale network research studies.

### Building the Pipeline: The ETL Process

The process of converting raw data into the OMOP CDM is known as ETL: Extract, Transform, and Load.

1. **Extract:** Pulling data from the source systems (e.g., patient, lab, and pharmacy databases).
2. **Transform:** Converting and mapping the source data into the OMOP CDM's standardized tables and vocabularies.
3. **Load:** Loading the newly structured data into a destination database where it can be used for analysis.

At Siriraj, this pipeline involves extracting data from various systems nightly into an MS SQL Server data warehouse. From there, a dedicated transformation tool is used to remodel the data into the OMOP format.

### Choosing the Right Tool: Siriraj's Journey from dbt to SQLMesh

While OHDSI provides some tools, the core ETL implementation is left to each institution because of the technical complexity and variance in database types, security, and logic.

Siriraj's data team evaluated several options for their transformation workflow:

- **Custom Scripts (SQL, Python):** Highly flexible but difficult to maintain and scale.
- **Commercial ETL Tools (e.g., MS SSIS):** Can be costly and often rely on GUI interfaces that are hard to manage for complex, code-heavy projects.
- **dbt (data build tool):** A popular open-source tool that Siriraj initially used. However, the team found limitations, noting that dbt acts more as a templating engine and doesn't fundamentally understand the SQL code, which can make debugging difficult.
- **SQLMesh:** A newer open-source tool designed to overcome dbt's limitations. Siriraj transitioned to SQLMesh because it offers a more efficient and reliable solution for managing complex OMOP conversions.

### A Closer Look at SQLMesh: Why It's a Game-Changer

SQLMesh was chosen because it was built specifically for data modeling and includes several advanced features that other tools lack:

- **SQL Semantic Understanding:** Unlike tools that just template text, SQLMesh parses and understands the SQL code itself. This allows it to catch bugs *before* a query is run on the database and to create detailed column-level lineage, tracking exactly how data flows and transforms.
- **Virtual Data Environments:** It allows developers to safely work in isolated development environments without interfering with the production data, even while using the same physical database.
- **Data Contracts & Audits:** SQLMesh can enforce data quality rules (audits) to ensure the transformations are complete and accurate before the data is promoted for use. If a transformation is incomplete, it blocks the flawed data from entering production.
- **SQL Transpilation:** It can automatically translate SQL syntax between different database systems (e.g., from Microsoft SQL Server to DuckDB), which is invaluable for migrations and collaboration.
- **Incremental Models:** For large datasets, SQLMesh can be configured to process only new or changed data, dramatically reducing run times from hours to minutes.

### Get Started and Join the Community

For those looking to begin their own OMOP ETL journey, the presenter offered a clear path forward:

1. **Start with the Docs:** Explore the SQLMesh documentation and "Get Started" guide.
2. **Try the Demo:** Siriraj has provided a public GitHub repository with a demonstration of converting synthetic data to OMOP using SQLMesh and DuckDB.
3. **Build Incrementally:** Start by converting simple but essential tables first, like `person` and `concept`, before moving to more complex ones.

The meetup concluded with a call to action to form an official **OHDSI Thailand Chapter**. This would create a formal network for sharing knowledge, supporting research, and increasing the visibility of Thai data science on the global stage. The next meetup is scheduled for **Thursday, July 24**, and will provide an "Overview of OHDSI".
