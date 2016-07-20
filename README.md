# More Studio API Guideline 1.0

## API Version

ใช้ API version ในการแยก API แต่ละ version ไม่ให้เกิดผิดพลาดในฝั่ง client ในการ map object

เมื่อมีการอัพเวอร์ชั่นต้องมั่นใจว่า structure เวอร์ชั่นก่อนหน้าไม่มีการเปลี่ยนแปลง

ตัวอย่าง
`/api/v1/:endpoint` 

## Endpoint

* การตั้งชื่อต้องเป็น nouns ในรูปพหูพจน์
* ชื่อต้องเป็นตัวเล็กทั้งหมด ห้ามเป็นตัวเล็กตัวใหญ่
* พยายามไม่ใช้ verbs ในการตั้งชื่อ

ตัวอย่าง

`/products`

`/users/1`

## HTTP Method
| GET    |   POST   |  PUT   | DELETE |
|--------|:--------:|-------:|-------:|
| Read   | Update   | Edit   | Delete |

* ใช้ `GET` ในการดึงข้อมูลเท่านั้น ห้ามใช้ create/update/delete
* หากเป็นไปได้ให้ใช้ Standard HTTP Method `(GET, POST, PUT, DELETE)`
* หากไม่สะดวกในการใช้ Standard HTTP Method ให้ใช้ `POST` ทั้งหมดยกเว้นการดึงข้อมูลให้ใช้ `GET`

## Parameters

* parameter ที่ใช้จะต้องเป็นตัวเล็กทั้งหมด ระหว่างคำคั่นด้วย `_ (underscore)` เช่น `product_name`


## Response Data

* ใช้ *JSON* 
* `status` มี 3 กรณีคือ *success, fail, error*
    * *success* ในกรณีเรียกใช้ API สำเร็จ
    * *fail* ในกรณีที่เกิดข้อผิดพลาดจากตัว user เช่น input invalid
    * *error* ในกรณีที่เกิดข้อผิดพลาดจากตัวระบบ
* key ที่ใช้จะต้องเป็นตัวเล็กทั้งหมด ระหว่างคำคั่นด้วย `_ (underscore)` เช่น `debug_message`
* หากเป็นไปได้ id ของ object ไม่ควรจะเป็น running number
* key จะต้องใช้ชื่อเดิมตลอด ไม่ว่าจะเรียกจาก API ไหนก็ตาม

### Structure

* ใน level - 0 จะมี key `status` และ `data`  ยกเว้นกรณี *error* ข้อมูลข้อผิดพลาดจะอยู่ใน `error`
* ใน level เดียวกันข้อมูลจะต้องเป็นข้อมูลของ object เดียวกันเท่านั้น หากเป็นของ object อื่นให้ผลักไปอีก level
* ในกรณีเป็นข้อมูลเดี่ยว เช่น product's detail ข้อมูลของ object จะอยู่ใน `data` ที่ level - 0 
* ในกรณีเป็นข้อมูลชุด เช่น list of products ข้อมูลของ object จะอยู่ใน `objects` ที่ level - 1
* ในกรณีเป็นข้อมูลชุด จะมี `total`, `page` ที่ level - 1
* `page` เริ่มที่ 1
* `error` จะประกอบไปด้วย `code`, `message`, `debug_message`
    * `code` คือ error code อาจจะใช้ร่วมกับ http status
    * `message` คือ ข้อความที่จะแสดงต่อ user ไม่ควรเป็นข้อความที่ได้จาก server โดยตรง
    * `debug_message` คือ ข้อความข้อผิดพลาดที่ได้รับจาก server โดยตรง เพื่อสะดวกในการ debug
    * `debug_message` จะไม่มีการส่งไปที่ client เมื่อเป็น production 
### ตัวอย่าง
*Success:*

Object

```json
{
    "status": "success",
    "data": {
        "id": "e530v5",
        "name": "name",
        "owner": {
            "id": "59vk4",
            "name": "name"
        },
        "products": [
            {
                "id": "39v9t",
                "name": "name of product"
            }
        ]
    }
}
```

Objects

```json
{
    "status": "success",
    "data": {
        "objects": [
            {
                "id": "e530v5",
                "name": "name"
            }, 
            {
                "id": "59bv9c",
                "name": "name"
            }
        ],
        "total": 2,
        "page": 1
    }
}
```

*Fail:*

```json
{
    "status": "fail",
    "data": {
        "title": "fail message"
    }
}
```

*Error:*

```json
{
    "status": "error",
    "error": {
        "code": 400, //int
        "message": "error message",
        "debug_message": "system error message"
    }
}
```

## Date Time Format

* format `yyyy-MM-dd HH:mm:ss` ตัวอย่าง `2016-12-31 23:59:00`
* ใช้ format นี้ในส่วนของ parameter และ response
* ปีที่ใช้เป็นคริสตศักราชเท่านั้น
* ใช้ UTC±0 ไม่มีการบวกลบเวลา
