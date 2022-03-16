
# Common Route 

1. เปิดไฟล์ **components/main-layout.tsx** และเพิ่ม Link component ลงไปใน JSX

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
            {children}
        </div>
    )
}

export default MainLayout
```

2. บันทึกไฟล์ และทดสอบการทำงานโดยการรัน `npm run dev` และกด link เพื่อเปิดไปยังหน้า About

