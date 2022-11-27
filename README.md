# GoFile.io

<h1 align="center">
  <img src="https://gofile.io/dist/img/logo-big.png" alt="php" width="120">
</h1>
<p align="center">GoFile API (outdated)</p>

<p align="center">
  <a href="https://opensource.org/licenses/MIT">
    <img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="License MIT">
  </a>
</p>


### UploadFile
To upload a file to *gofile.io* you must use *multipart / form-data*.

### New format to be able to upload
```python
from requests_toolbelt.multipart.encoder import MultipartEncoder
import aiohttp
import asyncio
import sys

import requests

async def encode_file(name:str) -> dict:
    mp_encoder = MultipartEncoder(
        fields={
            'filesUploaded': (name, open(name, 'rb'))
        }
    )
    return mp_encoder


async def get_server() -> str:
    url = "https://api.gofile.io/getServer"
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as resp:
            server = await resp.json()
            return server['data']['server']

async def upload_server(server:str) -> dict:
    try:
        url = f"https://{server}.gofile.io/uploadFile"
        data_json = await encode_file("music.m4a")

        #Unable to send file using async methodologies like aiohttp, some kind of weird error happens
        #It says the uploaded file format is incorrect
        #I still don't have a solution for this.
        #ERROR: Only io.IOBase, multidict and (name, file) pairs allowed, use .add_field() for passing more complex parameters
        response = requests.post(
            url,
            data=data_json, 
            headers={'Content-Type': data_json.content_type}
        )

        return response.json()

    except Exception as e:
        trace_back = sys.exc_info()[2]
        line = trace_back.tb_lineno
        print(f"ERROR UPLOAD -> {e} | {line}")

async def main() -> None:
    server_get = await get_server()
    response = await upload_server(server=server_get)
    print('LINK: ',response['data']['downloadPage'])

asyncio.run(main())

```


#outdated:
### Finding server

```
   method GET
   https://apiv2.gofile.io/getServer
```
Returns the best server available to receive uploads
Response example

```
{"status":"ok","data":{"server":"srv-file9"}}
```
### Parameters
- **required filesUploaded**<br>
formData files
Must contain one or multiple files.
- **email**<br>
formData string
Must contain an email address syntax. The upload will be stored on this account.
- **description**<br>
formData string
Must contain a description of the upload
- **password**<br>
formData string
Must contain a password of the upload
- **tags**<br>
formData string
Must contain the tags of the upload. If there are multiple tags, seperate them with comas (example : tags1,tags2)
- **expire**<br>
formData integer
Must contain an expiration date of the upload in a form of timestamp.

example in python
```python
    from requests_toolbelt.multipart.encoder import MultipartEncoder
    import requests
    import json

    mp_encoder = MultipartEncoder(
        fields={
            'filesUploaded': ('file_name.txt', open('file_name.txt', 'rb'))
        }
    )
    r = requests.post(
        'https://srv-file9.gofile.io/upload',
        data=mp_encoder, 
        headers={'Content-Type': mp_encoder.content_type}
    )
    scrap =  r.json()
    #send "Token file" 123Abc
    Token = scrap['data']['code']
```
  **Response example**
```
{"status":"ok","data":{"code":"123Abc","removalCode":"3ZcBq12nTgb4cbSwJVYY"}}
```
Your access link will look like this :`https://gofile.io/?c=123Abc`

### Validating your file
To validate the file and get the direct link, you will need to validate your token on the server, by sending it your token

example in python
```python
    import requests

    session = requests.Session()

    sendToken = 'https://apiv2.gofile.io/getServer?c={}'.format(Token)
    validating = 'https://srv-file9.gofile.io/getUpload?c={}'.format(Token)

    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.163 Safari/537.36 OPR/67.0.3575.137'}

    session.get(sendToken, headers=headers)
    session.get(validating, headers=headers)

    r = 'https://srv-file9.gofile.io/download/{}/file_name.txt'.format(Token)

```
Your access link will look like this: `https://srv-file9.gofile.io/download/123Abc/file_name.txt`

<br>

## Autor
- **Londarks** - Developer - [Twitter](https://twitter.com/londarks)
