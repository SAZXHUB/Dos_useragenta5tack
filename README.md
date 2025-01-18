import random
import requests
import platform
import threading

# ฟังก์ชันเพื่อสร้าง User-Agent แบบสุ่ม (Spoofing)
def generate_random_user_agent():
    # สุ่มเลือกระบบปฏิบัติการและเบราว์เซอร์ที่ปลอมแปลง
    os_platforms = [
        ("Windows NT 10.0", "Windows"), 
        ("Macintosh; Intel Mac OS X 10_15_0", "Darwin"), 
        ("Linux; Android 7.0; Nexus 5 Build/NBD92G", "Linux")
    ]
    os_platform, os_type = random.choice(os_platforms)
    
    browser_versions = ["58.0.3029.110", "61.0.3163.100", "67.0.3396.99", "79.0.3945.79", "78.0.3904.108"]
    browser_version = random.choice(browser_versions)
    
    # ปลอมแปลงการแสดงผลของเบราว์เซอร์และระบบปฏิบัติการ
    if os_type == "Windows":
        return f"Mozilla/5.0 ({os_platform}; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/{browser_version} Safari/537.36"
    elif os_type == "Darwin":
        return f"Mozilla/5.0 ({os_platform}) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/{browser_version} Safari/537.36"
    else:
        return f"Mozilla/5.0 ({os_platform}) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/{browser_version} Mobile Safari/537.36"

# ฟังก์ชันสำหรับการส่งคำขอพร้อม User-Agent แบบไม่มีการหน่วงเวลา
def send_request(website_url):
    user_agent = generate_random_user_agent()
    headers = {"User-Agent": user_agent}
    try:
        with requests.Session() as session:
            response = session.get(website_url, headers=headers)
            print(f"Requesting {website_url} with User-Agent: {user_agent} - Status Code: {response.status_code}")
    except requests.RequestException as e:
        print(f"Error accessing {website_url} with User-Agent: {user_agent} - Error: {e}")

# ฟังก์ชันหลักในการส่งคำขอหลายๆ ครั้งโดยใช้ Threading
def loop_generate_user_agents(website_url, count=100):
    threads = []
    for _ in range(count):
        thread = threading.Thread(target=send_request, args=(website_url,))
        threads.append(thread)
        thread.start()
    
    for thread in threads:
        thread.join()

# รับ input เว็บไซต์จากผู้ใช้
website = input("Enter the website URL: ")
num_requests = int(input("Enter the number of requests (e.g., 100): "))

# สร้างและส่งคำขอ User-Agent ไปยังเว็บไซต์
loop_generate_user_agents(website, num_requests)

