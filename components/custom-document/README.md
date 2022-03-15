
# Custom Document 

- Custom document คือความสามารถในการเข้าไปปรับแก้ attribute ในระดับ Document page เช่นใน `<Head>` หรือ `<Body>`
- การกำหนดค่าต่างๆ ใน `<Head>` ควรเป็นค่าที่ต้องการให้มีผลในทุก document และใช้ `next/head` ในแต่ละหน้าเพื่อกำหนดค่าเฉพาะหน้าอีกที (เช่น `title`)

1. สร้างไฟล์ **pages/_document.tsx**

```tsx

import Document, {DocumentContext, Html, Head, Main, NextScript} from 'next/document';
import { ReactElement } from 'react';

class CustomDocument extends Document {
  static async getInitialProps(ctx: DocumentContext) {
    const initialProps = await Document.getInitialProps(ctx);
    return initialProps;
  }
  render():ReactElement {
    // สังเกตว่ามีการกำหนด lang ใน <Head>
    // และ style ใน <body>
    return (
        <Html>
          <Head lang='en' />
          <body style={{backgroundColor: 'blue'}}>
            <Main />
            <NextScript />
          </body>
        </Html>
      )
  }
}

export default CustomDocument
```

## Note

- Component ใดๆ ที่อยู่นอก `<Main>` ในที่นี้จะไม่ถูก render 
- `<NextScript>` จำเป็นต้องมีเพื่อการทำงานของ Next.js ในทุก Document