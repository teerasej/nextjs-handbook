
# Using Layout

## 1. สร้าง Layout

1.1 สร้าง **components/footer.tsx**

```tsx
import { NextComponentType } from "next"


const Footer:NextComponentType = ({ children }) => {
    return (
        <div>
            <a href="https://www.nextflow.in.th">
                <p>Nextflow.in.th</p>
            </a>
        </div>
    )
}

export default Footer
```

1.2 สร้าง **components/main-layout.tsx**

```tsx
import { NextComponentType } from "next"
import Link from "next/link"
import Footer from "./footer"


const MainLayout:NextComponentType = ({ children }) => {
    return (
        <div>
            <div>
                <Link href={'/'}>Home</Link>
                <Link href={'/about'}>About</Link>
            </div>
            <div>{children}</div>
            <Footer/>
        </div>
    )
}

export default MainLayout
```

- สังเกตว่าเรากำหนด type ของ component เป็น `NextComponentType` ซึ่งเป็นตัวแทนของ Function component ใน Next.js เวอร์ชั่น 11 เป็นต้นไป
- สังเกตว่าเราวางไฟล์ทั้งหมดไว้ใน **/components** ไม่ใช่ **/pages**
   
## 2. กำหนด Layout ในระดับ global 

เปิดไฟล์ **pages/_app.tsx**

```tsx
import '../styles/globals.css'
import type { AppProps } from 'next/app'

// import MainLayout
import MainLayout from '../components/main-layout'

function MyApp({ Component, pageProps }: AppProps) {
  
  // ครอบ MainLayout ให้กับ Component ส่วนนี้จะมีผลกับ Page component ทุกตัว
  return (
    <MainLayout>
      <Component {...pageProps} />
    </MainLayout>
  )
}

export default MyApp

```

## 3. สร้างไฟล์ **pages/about.tsx**

สร้างไฟล์ page ใหม่ และทดสอบเข้าใช้งาน

```tsx
import { NextPage } from "next";
import styles from '../styles/Home.module.css'

const About:NextPage = () => {
    return (
        <div>
            <h1 className={styles.title}>About</h1>
        </div>
    )
}

export default About
```

## 4. สร้าง Layout component ใหม่ และนำไปใช้กับ page New-feature

4.1 สร้าง ไฟล์ **components/warning-layout.tsx**

```tsx

import { NextComponentType } from "next"


const WarningLayout:NextComponentType = ({ children }) => {

    return (
        <div>
            <div>
                <p style={{backgroundColor: 'yellow'}}>This feature is still in beta</p>
            </div>
            <div>{children}</div>
        </div>
    )
}

export default WarningLayout
```

4.2 สร้างไฟล์ page **pages/new-feature.tsx**

```tsx
import { NextPage } from "next";
import WarningLayout from "../components/warning-layout";
import styles from '../styles/Home.module.css'


const NewFeature:NextPage = () => {
    return (
        <WarningLayout>
        <div>
            <h1 className={styles.title}>New Feature</h1>
        </div>
        </WarningLayout>
    )
}

export default NewFeature;
```

4.3 เปิดไฟล์ **components/main-layout.tsx** เพื่อเพิ่ม link ไปยังหน้าเพจใหม่

```tsx
import { NextComponentType } from "next"
import Link from "next/link"
import Footer from "./footer"


const MainLayout:NextComponentType = ({ children }) => {
    
    // เพิ่ม link ไปยัง page new-feature
    return (
        <div>
            <div>
                <Link href={'/'}>Home</Link> |  
                <Link href={'/about'}>About</Link> | 
                <Link href={'/new-feature'}>New Feature</Link>
            </div>
            <div>{children}</div>
            <Footer/>
        </div>
    )
}

export default MainLayout
```

4.4 บันทึก และทดสอบการใช้งาน