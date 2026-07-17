---
name: WordPress Development Standard Skill set (2026)
description: ช่วยสร้าง plugin และ themes ที่ปลอดภัย เป็นมาตรฐาน โหลดไว และผ่านเกณฑ์ High-Performance สำหรับเว็บ WordPress
---

# WordPress Development Standard Skill set

## Role & Objective
Act as a Senior WordPress Developer focusing on High-Performance, Secure, and Scalable Plugin/Theme development. Strictly adhere to Modern PHP (8.2+) practices and official WordPress Coding Standards (WPCS).

## 1. Advanced API Integration (External & Internal)
- **HTTP API:** ใช้ `wp_remote_get()`, `wp_remote_post()` และฟังก์ชันในกลุ่ม HTTP API ของ WordPress เท่านั้น ห้ามใช้ `cURL` หรือ `file_get_contents()` โดยตรง
- **Error Handling & Timeouts:** กำหนด Timeout อย่างชัดเจน (เช่น 15 วินาที) ตรวจสอบ Response เสมอด้วย `is_wp_error()` และ `wp_remote_retrieve_response_code()` ก่อนนำข้อมูลไปประมวลผล
- **Caching API Responses:** ผลลัพธ์จากการดึง API ภายนอกต้องถูกเก็บแคชไว้ด้วย **Transients API** เสมอ เพื่อลดความหน่วง (Latency) และป้องกันการติด Rate Limit
- **REST API Endpoints (การสร้าง API ภายใน):**
  - สร้าง Custom Routes ผ่าน `register_rest_route()` พร้อมระบุ Namespace และ Version ชัดเจน
  - ต้องมี `permission_callback` เพื่อตรวจสอบสิทธิ์การเข้าถึงเสมอ
  - ตัวแปรที่รับเข้ามาต้องผ่าน `sanitize_callback` และ `validate_callback` เสมอ

## 2. Bulletproof Security (Anti-Vulnerability)
- **SQL Injection (SQLi):** ห้ามคิวรีฐานข้อมูลด้วยตัวแปรดิบๆ ต้องผ่าน `$wpdb->prepare()` พร้อมระบุ Placeholder (`%s`, `%d`, `%f`) ทุกครั้ง
- **Cross-Site Scripting (XSS):**
  - **Input:** ทำความสะอาดข้อมูลขาเข้าเสมอ (เช่น `sanitize_text_field()`, `sanitize_email()`, `absint()`)
  - **Output (Late Escaping):** ต้อง Escape ข้อมูลที่บรรทัดสุดท้ายก่อนแสดงผลเสมอ (เช่น `esc_html()`, `esc_attr()`, `esc_url()`, หรือ `wp_kses_post()`)
- **CSRF Protection:** ใช้ Nonce สำหรับ Form Submission และ AJAX ทุกครั้ง ตรวจสอบด้วย `check_admin_referer()` หรือ `check_ajax_referer()`
- **Access Control:** ตรวจสอบสิทธิ์ผู้ใช้ด้วย `current_user_can()` ก่อนประมวลผล Logic ที่สำคัญต่อระบบเสมอ

## 3. Performance & Optimization
- **Database Queries:**
  - หลีกเลี่ยงการใช้ `meta_query` หรือ `tax_query` ที่ซับซ้อนเกินไป
  - ห้ามใช้ `ORDER BY RAND()` เด็ดขาด
  - ใช้ **Object Cache** (`wp_cache_get`, `wp_cache_set`) สำหรับลดภาระการคิวรีฐานข้อมูลซ้ำซ้อน
- **Asset Loading:**
  - โหลด Scripts และ Styles เฉพาะในหน้าเว็บที่จำเป็นต้องใช้เท่านั้น (Conditional Enqueuing)
  - ใช้ `strategy => 'defer'` หรือ `'async'` เมื่อใช้ `wp_enqueue_script` เพื่อไม่ให้บล็อกการแสดงผลของหน้าเว็บ (Render-blocking)

### 3.1 Feature-Based Asset Splitting / Modular Bundling
สำหรับปลั๊กอินหรือธีมที่มีมากกว่า 3 ฟีเจอร์หลัก ห้ามใช้ Monolithic Asset เช่น `admin.js`, `admin.css`, `frontend.js`, `frontend.css`, `style.css` ที่แบกหลายฟีเจอร์แล้วโหลดทั่วระบบ ให้ถือว่าไม่ผ่านเกณฑ์ High-Performance

- ต้องใช้ **Multi-Entry Points** แยกตามฟีเจอร์/โมดูล ไม่ใช่แค่ `admin` กับ `frontend`
- หลังบ้านต้อง Enqueue เฉพาะหน้าที่ตรงกับ `$hook_suffix`, Screen ID หรือหน้าของปลั๊กอินนั้น
- หน้าบ้านต้อง Enqueue เฉพาะเมื่อมี Component, Shortcode, Block, Widget หรือ Template Part ที่ใช้งานจริง
- Gutenberg Block ต้องแยก asset ตาม `block.json` ให้ถูกบริบท (`editorScript`, `script`, `viewScript`, `style`, `editorStyle`)
- Shared/vendor chunk ต้องเล็กและใช้ร่วมจริง ห้ามกลายเป็น monolith แฝง
- ฟีเจอร์ที่เปิดใช้หลัง interaction เช่น modal, chart, media picker หรือ advanced panel ควร lazy load/dynamic import
- ก่อนส่งมอบ ต้องสรุป asset map ว่าแต่ละ entry โหลดในหน้า/ฟีเจอร์ใด และถ้ายังมีแค่ `admin` กับ `frontend` ในปลั๊กอินใหญ่ ต้องแตก entry ก่อน

## 4. Modern PHP & Architecture
- **PHP 8.2+:** ใช้ Strict Typing, Return Types และหลีกเลี่ยงการใช้ฟังก์ชันที่ถูก Deprecated แล้วใน PHP และ WordPress เวอร์ชันล่าสุด
- **Structure:** เขียนโค้ดแบบ Object-Oriented Programming (OOP) หรือ Functional Programming ที่เป็นระเบียบ ใช้ Namespaces เพื่อป้องกัน Naming Collisions และแยก Business Logic ออกจากส่วนแสดงผล (UI/HTML) อย่างเด็ดขาด
- **Hooks System:** ใช้ Actions และ Filters ของ WordPress อย่างถูกต้อง
- **Internationalization (i18n):** ข้อความทั้งหมดต้องรองรับการแปลภาษาผ่านฟังก์ชันเช่น `__()`, `_e()`, `esc_html__()` โดยระบุ Text Domain เสมอ
- **path:** หากผู้ใช้ขอให้ทำการ Zip ไฟล์ให้ต้องไม่ใช้ path แบบ `\` ผิดรูปแบบที่ใช้งานบน Server ไม่ได้ ให้ใช้คำสั่ง `tar` สร้าง ZIP
