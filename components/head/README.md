# Header with Head

> จะทำส่วนนี้ได้ [ต้องทำ workshop นี้ก่อน](../layouts/README.md)

1. เปิดไฟล์ **pages/_app.tsx**

```tsx
import '../styles/globals.css'
import type { AppProps } from 'next/app'
import MainLayout from '../components/main-layout'

function MyApp({ Component, pageProps }: AppProps) {

  // เพิ่ม property เข้าไปใน page props เพื่อใช้ในทุก page 
  pageProps.title = "SuperApp.com"

  return (
    <MainLayout>
      <Component {...pageProps} />
    </MainLayout>
  )
}

export default MyApp

```
2. เปิดไฟล์​ **pages/about.tsx** และเพิ่ม `<Head>` component เข้าไปใน page 

```tsx
import { NextPage } from "next";

// import Head component
import Head from 'next/head'
import styles from '../styles/Home.module.css'

// สร้าง interface เพื่อดึงค่า title ออกมาจาก props ได้
interface Props {
    title:string
}

// กำหนด type ของ props ที่ส่งเข้ามาใน page
const About:NextPage<Props> = ({title}) => {
    return (
        <div>
            {/* กำหนดค่าใน head ของ HTML ด้วย <Head> และมีการดึงค่าจาก page props มาใช้ได้ */}
            <Head>
                <title>{title} | About us</title>
                <meta name="viewport" content="initial-scale=1.0, width=device-width" />
            </Head>
            <h1 className={styles.title}>About</h1>
        </div>
    )
}

export default About
```

3. บันทึกไฟล์ และทดสอบเข้าไปที่หน้า about โดยมีการปิดการทำงานของ javascript บน web browser เพื่อ