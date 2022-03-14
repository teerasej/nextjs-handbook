
# Simple Page Rendering

1. สร้างโปรเจคใหม่ด้วยการรันคำสั่งใน Terminal ด้านล่าง โดยระบบจะถามชื่อโปรเจคเพื่อใช้ในการกำหนดชื่อโฟลเดอร์

```bash
npx create-next-app@latest --ts
```

2. สร้างไฟล์ `pages/StaticGenerationPost.tsx`

```tsx

import { NextPage } from 'next'
import React from 'react'


const StaticGenerationPost: NextPage = () => {
  return (
    <div>StaticGenerationPost</div>
  )
}

export default StaticGenerationPost
```
- สังเกตว่ามีการกำหนด type ของ  Function component เป็น `NextPage`

3. เปิดไฟล์ `pages/index.ts` และแก้ไขไฟล์เป็นแบบด้านล่าง เพื่อเพิ่ม link ลงไปในหน้าเว็บ 

```tsx
import type { NextPage } from 'next'
import Link from 'next/link'
import styles from '../styles/Home.module.css'

const Home: NextPage = () => {
  return (
    <div className={styles.container}>
        <a>
          <Link href={'/StaticGenerationPost'}>Static Generation</Link>
        </a>
    </div>
  )
}

export default Home
```
- สังเกตว่า `href` กำหนดเป็นชื่อที่สอดคล้องกับชื่อไฟล์ที่สร้างไว้


4. รันทดสอบแอพด้วยคำสั่งด้านล่างและกด link ไปที่ http://localhost:3000/ และกดลิ้งค์ไปยังหน้าเพจที่เตรียมไว้

(หากมีการเปลี่ยน port ของ Nextjs ให้เปลี่ยน url ด้านบนให้ตรงกับ port ของตัวเอง)

```bash
npm run dev
```

5. เปิดดู source code ของหน้าเว็บ เพื่อดูโค้ด HTML ที่สร้างขึ้น