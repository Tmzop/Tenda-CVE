# NAME OF AFFECTED PRODUCT(S) 
- Tenda Router BE3L Pro  V16.03.60.62 - Buffer Overflow in `/goform/WifiBasicSet`

---

### Vulnerability Details
| **Detail** | **Information** |
| :--- | :--- |
| **Vendor** | Shenzhen Jixiang Tengda Technology Co., Ltd. |
| **Product** | Router BE3L Pro |
| **Affected Version** | V16.03.60.62 |
| **Vulnerability Type** | Buffer Overflow |
| **Vendor Homepage** | `https://www.tenda.com.cn/` |


---
<img width="865" height="409" alt="image" src="https://github.com/user-attachments/assets/dda13419-d99e-4ae3-93f1-8e147312b16f" />
<img width="865" height="109" alt="image" src="https://github.com/user-attachments/assets/efc9b1df-9602-4a7d-b51e-8d60c52e543f" />


### Vulnerability Description
During the security assessment of the application, a critical buffer overflow vulnerability was identified in the  /goform/WifiBasicSet endpoint. The vulnerability stems from the formWifiBasicSet() function, This function retrieves the security_5g parameter from a POST request.The strcpy function does not check the size of the target buffer when processing the security_5g parameter. Since *s1 is limited to 256 bytes, providing input larger than this size can overwrite adjacent memory. This flaw can lead to application crashes, memory corruption, or arbitrary code execution. The vulnerability introduces serious risks to device stability, data confidentiality, and overall system security, and requires immediate remediation to prevent potential exploitation.

---
## Vulnerability location:
-  /goform/WifiBasicSet
---

### Root Cause
A buffer overflow vulnerability was identified within the " /goform/WifiBasicSet" endpoint of the application. The root cause lies in the fact that the formWifiBasicSet() function processesThis function retrieves the security_5g parameter from a POST request.The strcpy function does not check the size of the target buffer when processing the security_5g parameter . Since *s1 is allocated with a fixed size of 256 bytes, supplying an excessively large page value can overwrite adjacent memory. This unsafe use of strcpy allows attackers to trigger memory corruption, manipulate program execution, or cause application crashes.
<img width="781" height="26" alt="image" src="https://github.com/user-attachments/assets/5bfb7706-81ef-4c6c-9094-fb80ad5d1739" />
<img width="725" height="769" alt="image" src="https://github.com/user-attachments/assets/492ef819-13b2-49ac-965d-61361f0948fb" />
<img width="859" height="388" alt="image" src="https://github.com/user-attachments/assets/ff4cecf6-9862-403d-857c-56a8fa42ff95" />
### Impact
An attacker can exploit this vulnerability to achieve various malicious outcomes, including:
-   **Denial of Service (DoS):** Crashing the web server process and making the device's management interface inaccessible.
-   **Arbitrary Code Execution:** Overwriting the return address on the stack to redirect program execution to shellcode, potentially allowing the attacker to gain full control over the device.
-   **Information Leakage:** Exposing sensitive information from the device's memory.

Successful exploitation could allow an attacker to take over the router, monitor network traffic, or use it as a pivot point to attack other devices on the network.

---

### Proof of Concept (PoC)
This vulnerability can be triggered without any authentication. The following Python script demonstrates the exploit by sending a POST request with an oversized `page` parameter.

```python
import requests

url = "http://192.168.15.135/goform/WifiBasicSet"
payload = {
     'security_5g':b'a'*2048
    }
res = requests.post(url=url,data=payload)

```
## The following are screenshots of the local reproduction
- Setting up the environment
<img width="772" height="532" alt="image" src="https://github.com/user-attachments/assets/11d8db97-a166-4a18-8a5f-b893afe6cf0d" />
- Running the poc.py script
<img width="791" height="369" alt="image" src="https://github.com/user-attachments/assets/c2dc4528-ba47-4804-af21-c7a84a26501e" />
<img width="865" height="617" alt="image" src="https://github.com/user-attachments/assets/4c6f6a22-2f61-49de-81d4-f8c747e8d4ed" />
<img width="865" height="485" alt="image" src="https://github.com/user-attachments/assets/987292c6-7043-4fc9-a9e5-14a517364a63" />



## Suggested repair

1. Use safer output functions:
Replace unsafe functions such as strcpy with safer alternatives like strncpy, which enforce buffer size limits and prevent writing beyond allocated memory.

2. Implement strict bounds checking:
Always check the length of user input (security_5g parameter) before processing, ensuring it does not exceed the fixed buffer size of 256 bytes. Explicitly truncate or reject oversized input.

3. Validate and sanitize input:
Enforce strict validation rules for the page parameter to ensure it conforms to expected formats and lengths, discarding or rejecting malformed or excessively large inputs.

4. Apply least privilege principle:
Run the service with the lowest required privileges, minimizing potential damage in the event of successful exploitation. For network devices, restrict access to sensitive memory and configuration areas.

5. Adopt secure coding practices:
Encourage the use of memory-safe programming techniques and safer APIs to reduce the likelihood of buffer overflow vulnerabilities in future code development.
