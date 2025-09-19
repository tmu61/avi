#!/usr/bin/python3
import sys,os,requests,json
from getpass import getpass

def GET(url,prepend=True):
    global avi_controller,json_data,json_text
    print("GET " + url, file=sys.stderr)
    if prepend:
        req_url=avi_controller+url
    else:
        req_url=url
    print(req_url, file=sys.stderr)
    response = session.get(req_url, headers=headers, verify=False, timeout=12000)
    if response.status_code == 200:
        print("GET succeeded", file=sys.stderr)
        json_data = response.json()
        json_text = response.text
    else:
        print("GET failed", response.text, file=sys.stderr)
    return(response.status_code)

def GETX(url):
    global avi_controller,json_data,json_text
    print(url, file=sys.stderr)
    req_url=avi_controller+url
    print(req_url, file=sys.stderr)
    response = session.get(req_url, headers=headers, verify=False, timeout=12000)
    if response.status_code == 200:
        print("GET succeeded", file=sys.stderr)
        json_text = response.text
        print(json_text)
    else:
        print("GET failed", response.text, file=sys.stderr)
    return(response.status_code)

def PUT(json_file):
    global avi_controller,session,headers
    with open(json_file) as json_data:
        d=json.load(json_data)
        json_data.close
    json_print(d)
    print(d["url"], file=sys.stderr)
    update_url=d["url"]
    response = session.put(update_url, json=d, headers=headers, verify=False)
    print("status",response.status_code, file=sys.stderr)

def POST(url,json_file):
    with open(json_file) as json_data:
        d=json.load(json_data)
        json_data.close
    url=f"{avi_controller}{url}"
    print(url,file=sys.stderr)
    response=session.post(url, json=d, headers=headers, verify=False)
    if response.status_code == 201:
        print("success")
    else:
        print("post returned ", response.status_code)
        print(response.text)

def DELETE(url):
    global avi_controller,session,headers
    req_url=avi_controller+url
    if (url.find("?name=")!=-1):
        print("found ?name=", file=sys.stderr)
        print(req_url, file=sys.stderr)
        response = session.get(req_url, headers=headers, verify=False, timeout=12000)
        o=response.json()["results"][0]['url']
        req_url=o
    print(req_url)
    response=session.delete(req_url,headers=headers,verify=False)
    print("delete status",response.status_code, file=sys.stderr)



def AUTH():
    global avi_controller,session,headers
    if "TENANT" in os.environ:
        TENANT=os.environ['TENANT']
    else:
        TENANT="*"
    if "REST_ENDPOINT" not in os.environ:
        print("You need to set the environment variable REST_ENDPOINT\n  Example: avi.company.org", file=sys.stderr)
        exit(2)
    REST_ENDPOINT=os.environ['REST_ENDPOINT']
    avi_controller = "https://"+REST_ENDPOINT
    login_url = f"{avi_controller}/login"
    if "AVI_USER" in os.environ and "AVI_PASS" in os.environ:
        username=os.environ['AVI_USER']
        password=os.environ['AVI_PASS']
    else:
        credsfile=os.environ['HOME']+"/creds/"+REST_ENDPOINT+".auth"
        if os.path.isfile(credsfile):
            f=open(credsfile,"r")
            creds=f.readline()
            username,password=creds.rstrip().split(":")
        else:
            print("The file "+credsfile+" doesn't exist", file=sys.stderr)
            username=input("Username for "+REST_ENDPOINT+": ")
            password=getpass()
    auth_payload = {
        "username": username,
        "password": password
    }
    # Disable SSL warnings
    requests.packages.urllib3.disable_warnings()

    session = requests.Session()
    response = session.post(login_url, json=auth_payload, verify=False)

    if response.status_code == 200:
        print("Successfully authenticated", file=sys.stderr)
        # Retrieve CSRF token from response cookies
        csrf_token = response.cookies['csrftoken']
    else:
        print("Authentication failed", response.text, file=sys.stderr)
        exit()

    headers = {
        "X-CSRFToken": csrf_token,
        "Referer": avi_controller,
        "X-Avi-Tenant": TENANT
    }

def json_print(json_obj):
     print(json.dumps(json_obj, indent=4))

