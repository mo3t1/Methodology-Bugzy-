# Methodology-Bugzy-

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
       
2. Extract old URLs from wayback
   - cat subs.txt | waybackurls > wayback.txt
3. Filter for  parameters
   - cat wayback.txt | grep "=" > params.txt
   - we'll later test for XSS,IDOR,LFI,Redirects using params.txt


     #To be updated

.      
