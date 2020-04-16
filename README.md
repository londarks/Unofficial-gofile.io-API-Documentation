# GoFile.io

<h1 align="center">
  <img src="https://gofile.io/dist/img/logo-big.png" alt="php" width="120">
</h1>
<p align="center">GoFile API</p>

<p align="center">
  <a href="https://opensource.org/licenses/MIT">
    <img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="License MIT">
  </a>
</p>


### UploadFile
To upload a file to *gofile.io* you must use *multipart / form-data*.

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
- **Londarks** - _Developer & Member of He4rt Developers_ - [Twitter](https://twitter.com/londarks)