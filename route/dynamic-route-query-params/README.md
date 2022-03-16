
# Dynamic Route & Query Parameter

> จะทำส่วนนี้ได้ [ต้องทำ workshop นี้ก่อน](../common-route/README.md)


## 1. เพิ่ม Link Menu

เปิดไฟล์ **components/main-layout.tsx** และเพิ่ม Link component ลงไปใน JSX

```tsx
import { NextComponentType } from "next";
import Link from "next/link";


const MainLayout: NextComponentType = ({children}) => {
    return (
        <div>
            <Link href="/">
                <a>Home</a>
            </Link>
             | 
            <Link href="/about">
                <a>About</a>
            </Link>
             | 
            <Link href="/annual-report">
                <a>Annual Report</a>
            </Link>
            {children}
        </div>
    )
}

export default MainLayout
```

## 2. สร้าง Type เพื่อใช้กับ Params 

สร้างไฟล์ **types/month-day.ts** เพื่อเอามาใช้ในการทำงานกับ query

```ts
export type QueryMonthDay = {
    month:string,
    day:string
}
```

## 3. สร้าง Dynamic route page

สร้างไฟล์​ **pages/annual-report/[month].tsx**

- route url ที่ match จะเข้าที่ไฟล์นี้ เช่น
  - /annual-report/abc
  - /annual-report/1234
- เสร็จแล้วลองทดสอบใช้งานดู

```tsx

import { NextPage } from "next"
import Link from "next/link"
import { useRouter } from "next/router"
import styles from '../../styles/Home.module.css'

// เรียกใช้ type ที่สร้างไว้
import type { QueryMonthDay } from "../../types/month-day"

const AnnualReportMonth: NextPage = () => {

  // เรียกใช้ router hook
  const router = useRouter()
  // ดึงค่า month ออกมาจาก query ของ router
  const { month } = router.query as QueryMonthDay

  return (
    <div>
      <main className={styles.main}>
        <h1 className={styles.title}>
          Annual Report: {month}
        </h1>
      </main>
    </div>
  )
}

export default AnnualReportMonth
```

## 4. สร้าง Dynamic route page แบบ Nested

สร้างไฟล์ สร้างไฟล์​ **pages/annual-report/[month]/[day].tsx**

- route url ที่ match จะเข้าที่ไฟล์นี้ เช่น
  - /annual-report/abc/123
  - /annual-report/1234/abc
- เสร็จแล้วลองทดสอบใช้งานดู

```tsx

import { NextPage } from "next"
import Link from "next/link"
import { useRouter } from "next/router"
import styles from '../../../styles/Home.module.css'

import { QueryMonthDay } from "../../../types/month-day"

const AnnualReportMonthDay: NextPage = () => {
  
  // เรียกใช้งาน router
  const router = useRouter()

  // เรียกใช้ค่า monty และ day ตาม url ที่วางไว้
  const { month, day } = router.query as QueryMonthDay

  return (
    <div>
      <main className={styles.main}>
        <h1 className={styles.title}>
          Annual Report: {month} {day}, 2022
        </h1>
      </main>
    </div>
  )
}

export default AnnualReportMonthDay
```