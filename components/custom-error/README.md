
# Custom Error 


- Custom error คือความสามารถในการเข้าไปปรับแต่งหน้า error ให้ตรงกับความต้องการ

## 1. สร้าง custom error component 

สร้างไฟล์ **pages/_error.tsx**

```tsx

import { NextApiResponse, NextPage, NextPageContext } from "next";


// กำหนด type ของ status code 
interface ErrorInfo {
    statusCode:string|undefined
}

// กำหนด ErrorInfo เป็น type หนึ่งของ props ที่ส่งเข้ามาใน component
const Error:NextPage<ErrorInfo> = ({ statusCode }) => {

  return (
    <p>
      {statusCode
        ? `An error ${statusCode} occurred on server`
        : "An error occurred on client"}
    </p>
  );
};

// กำหนด return type ของ async function ให้สอดคล้องกับ type ของ NextPage
Error.getInitialProps = async ({ res, err }: NextPageContext ):Promise<ErrorInfo> => {
  const code = res ? res.statusCode : err ? err.statusCode : 404;
  return { 
      statusCode:code?.toString() 
  };
};

export default Error;
```

## 2. เรียกใช้ Error ใน Page ตามต้องการ 

```tsx
const Home: NextPage = () => {

  if(true) return <Error statusCode='401'/>

  ...
```

## 3. ทดสอบ

custom error จะสามารถทำงานได้ใน production เท่านั้น ดังนั้นอย่าลืมทดสอบโดยรันผ่าน

```bash
npm run build
npm run start

// แทน npm run dev
```