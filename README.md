# Methodology-Bugzy

Recon && Testing Flow

1. Find subdomains

    Subfinder
     - subfinder -d target.com -o subs.txt

     Assetfinder
     - assetfinder --subs-only target.com >> subs.txt

     Amass passive
     - amass enum -passive -d target.com >> subs.txt

     Filter & Clean
     - cat subs.txt | sort -u > final_subs.txt
       
3. Extract old URLs from wayback
   - cat subs.txt | waybackurls > wayback.txt
4. Filter for  parameters
   - cat wayback.txt | grep "=" > params.txt
   - we'll later test for XSS,IDOR,LFI,Redirects using params.txt

5. Directory Brute-Forcing

# Gobuster
   -gobuster dir -u https://target.com -w wordlists/common.txt -t 50 -x php,js,html,json

# Dirsearch
   -python3 dirsearch.py -u https:/target.com -e php,json,js -x 403,404 -t 50
Common finds:

/internal/

/admin/

/debug/

/api/docs/

Header Fuzzing      
   
   Header fuzzing is used to identify misconfigurations, bypasses, and vulnerabilities in how a web server handles HTTP headers.

   1. Basic Header Inspection
      Use Burp Suite (Proxy tab) or curl:

          -curl -I https://platform.openai.com -o Basic-Header-Inspection.txt

      Look for:

      Server / CDN info

      Caching headers (Cache-Control)

      Auth headers

      CSP, CORS, X-Frame-Options
 
 2. Common Headers to Fuzz

    Request Spoofing / SSRF

           Try injecting these headers:

                X-Forwarded-For: 127.0.0.1
                X-Real-IP: 127.0.0.1
                X-Forwarded-Host: target.com
                X-Original-URL: /admin
                Forwarded: for=127.0.0.1;proto=http;host=internal

           Use Burp Repeater or curl:

                curl -H "X-Forwarded-For: 127.0.0.1" https://target.com/internal -o SSRF.txt

           Goal: Trigger access to internal endpoints / SSRF / IP-based bypasses.

    Cache Poisoning Headers

          Try injecting:

               X-Original-URL: /
               X-Forwarded-Host: evil.com
               X-Rewrite-URL: /

          Check for inconsistencies:

               Do you see mismatched responses?

               Does your payload show up for other users?

          Use Burp's Turbo Intruder or Cache Poisoning extension.


    CORS Misconfigurations

         Inject:

             Origin: https://evil.com
             Referer: https://evil.com

         Look for:

             Access-Control-Allow-Origin: *
             Access-Control-Allow-Origin: https://evil.com

         If Access-Control-Allow-Credentials is also true — potential CORS issue.

         WAF/Bypass/Verb Tampering

             Try alternate methods:

                curl -X HEAD https://target.com
                curl -X TRACE https://target.com
                curl -X OPTIONS https://target.com

             You can also fuzz for custom verbs:

                ffuf -X FUZZ -w verbs.txt -u https://chat.openai.com

   Advanced Header Tricks

       Host Header Injection

            Host: evil.com
            X-Forwarded-Host: evil.com

            Goal: Inject into password reset links or redirect flows

       Smuggling Techniques

          Look for vulnerable CL.TE or TE.CL configurations:

          python3 smuggler.py -u https://platform.openai.com


 Automated Header Fuzzing with httpx    

       cat subs.txt | httpx -H "X-Forwarded-For: 127.0.0.1" -title -status-code -web-server

       Combine with:

       ffuf -u https://target.com -H "FUZZ: payload" -w headers.txt

Combine headers (Origin + Referer + X-Forwarded-For)

Watch for redirect behavior

Look at server error messages

Log everything with Logger++ in Burp

###########To be edited##########
       
