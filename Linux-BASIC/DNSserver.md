# DNS
![](pic/dns.png)

1. 使用者開啟 Web 瀏覽器，在網址列輸入 www.example.com，然後按 Enter。
2. www.example.com 的請求路由到 DNS 解析程式，這通常由使用者的網際網路服務供應商 (ISP) 管理，例如有線電視網際網路供應商、DSL 寬頻供應商或公司網路。
3. ISP 的 DNS 解析程式將 www.example.com 的請求轉送到 DNS 根名稱伺服器。
4. ISP 的 DNS 解析程式再次轉送 www.example.com 的請求，這次轉送到 .com 網域的其中一個 TLD 名稱伺服器。.com 網域的名稱伺服器會以與 example.com 網域關聯的四部 Amazon Route 53 名稱伺服器名稱回應請求。
5. ISP 的 DNS 解析程式選擇一部 Amazon Route 53 名稱伺服器，並將 www.example.com 的請求轉送到該名稱伺服器。
6. Amazon Route 53 名稱伺服器會在 example.com 託管區域中尋找 www.example.com 記錄，取得關聯的值，例如 Web 伺服器的 IP 地址 192.0.2.44，然後將 IP 地址傳回 DNS 解析程式。
7. ISP 的 DNS 解析程式最終取得使用者需要的 IP 地址。解析程式將該值傳回 Web 瀏覽器。DNS 解析程式也會將 example.com 的 IP 地址快取 (存放) 您所指定的一段時間，下次有人瀏覽到 example.com 時即可更快速的做出回應。如需詳細資訊，請參閱存留時間 (TTL)。
8. Web 瀏覽器將 www.example.com 的請求傳送到從 DNS 解析程式取得的 IP 地址。這就是您的內容所在之處，例如，在 Amazon EC2 執行個體上執行的 Web 伺服器或是設定為網站端點的 Amazon S3 儲存貯體。
9. 位於 192.0.2.44 的 Web 伺服器或其他資源將 www.example.com 的網頁傳回 Web 瀏覽器，然後 Web 瀏覽器就會顯示該頁面。