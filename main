# -*- coding: utf-8 -*-
import requests
from datetime import datetime
from urllib.parse import quote
from PIL import Image
from aip import AipOcr
import io
import json
from flask import Flask
app = Flask(__name__)
# starturl = "http://iir.circ.gov.cn/ipq/insurance.do?checkcaptch&time=Thu%20May%2009%202019%2010:31:01%20GMT+0800%20(%E4%B8%AD%E5%9B%BD%E6%A0%87%E5%87%86%E6%97%B6%E9%97%B4)"
# #
hearder  = {
    "Accept": "application/json, text/javascript, */*; q=0.01",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36",
}

apcurl = "http://iir.circ.gov.cn/ipq/captchacn.svl"

request_url = "http://iir.circ.gov.cn/ipq/insurance.do?checkcaptch&time="
# 获取验证码以及cookie
def getapc(session,url):
    response = session.get(url, headers=hearder)
    print(response.cookies)
    with open("apc.jpg","wb") as file:
        file.write(response.content)


def getinfo(session,name,cardno):
    curr_time = datetime.strftime(datetime.now(),"%a %b %d %Y %H:%M:%S ") + "GMT+0800 (中国标准时间)"
    url = request_url + quote(curr_time)
    apc = baidu_img_str()
    if apc:
        response = session.post(url,data={
                        "ualificano":"",
                        "practicecode":"",
                            "captcha": apc,
                            "name": name,
                            "cardno": cardno
                            },headers=hearder)
        json.loads(response.text)
    else:
        print("验证码识别失败")


def main(session,apcurl,name,cardno):
    getapc(session, apcurl)
    getinfo(session,name,cardno)


def baidu_img_str():
    APP_ID = "??"
    API_KEY = "??"
    SECRET_KEY = "??"
    client = AipOcr(APP_ID, API_KEY, SECRET_KEY)
    img_byte_arr = io.BytesIO()
    image = Image.open("apc.jpg")
    image.save(img_byte_arr, format='PNG')
    image_data = img_byte_arr.getvalue()
    response = client.basicAccurate(image_data)
    print(response)
    result = response.get("words_result")
    if result:
        return result[0].get('words').replace(" ","")
    return ""


if __name__ == '__main__':
    session = requests.session()
    main(session,apcurl,"郭丽平",5626)
