# jeawen
Instagram Unfollow Checker v12 – jeawe_n" 
https://github.com/jeawen818/jeawen
import requests, json, os, time

os.system('clear')
print("\033[96m")
print("   ██╗███████╗ █████╗ ██╗    ██╗███████╗")
print("   ██║██╔════╝██╔══██╗██║    ██║██╔════╝")
print("   ██║█████╗  ███████║██║ █╗ ██║█████╗  ")
print("   ██║██╔══╝  ██╔══██║██║███╗██║██╔══╝  ")
print("   ██║███████╗██║  ██║╚███╔███╔╝███████╗")
print("\033[91m         ███╗   ██╗     Public Tool")
print("\033[93m             instagram.com/jeawe_n\033[0m\n")

target = input("\033[95m[?] Kullanıcı adı gir: @\033[0m")

url = f"https://www.instagram.com/{target}/?__a=1&__d=dis"
headers = {"User-Agent": "Mozilla/5.0 (Linux; Android 10; K) AppleWebKit/537.36"}

try:
    r = requests.get(url, headers=headers, timeout=10)
    data = r.json()
    user_id = data["graphql"]["user"]["id"]
except:
    print("\033[91m[!] Hesap gizli ya da yok kanka!\033[0m")
    exit()

followers = []
following = []

def get_list(type, cursor=None):
    variables = {"id": user_id, "include_reel": False, "fetch_mutual": False, "first": 50}
    if cursor: variables["after"] = cursor
    params = {"query_hash": "c76146de99bb02f6415203ff91f5c7d1" if type=="followers" else "d04b0a864b4b54837c0d870b0e77e076", "variables": json.dumps(variables)}
    return requests.get("https://www.instagram.com/graphql/query/", params=params, headers=headers).json()

print("\033[93m[!] Veriler çekiliyor sabret...\033[0m")
cursor = None
while len(followers) < 500:
    res = get_list("followers", cursor)
    for edge in res["data"]["user"]["edge_followed_by"]["edges"]:
        followers.append(edge["node"]["username"])
    if not res["data"]["user"]["edge_followed_by"]["page_info"]["has_next_page"]: break
    cursor = res["data"]["user"]["edge_followed_by"]["page_info"]["end_cursor"]
    time.sleep(1)

cursor = None
while len(following) < 500:
    res = get_list("following", cursor)
    for edge in res["data"]["user"]["edge_follow"]["edges"]:
        following.append(edge["node"]["username"])
    if not res["data"]["user"]["edge_follow"]["page_info"]["has_next_page"]: break
    cursor = res["data"]["user"]["edge_follow"]["page_info"]["end_cursor"]
    time.sleep(1)

unfollow = [x for x in following if x not in followers]

print("\033[92m\n╔══════════════════════════════════╗")
print(f"║  {len(unfollow)} kişi seni takmıyor kanka!  ║")
print("╚══════════════════════════════════╝\033[0m")
for i, user in enumerate(unfollow, 1):
    print(f"\033[91m[{i}] {user}\033[0m")

with open(f"{target}_unfollow.txt", "w") as f:
    f.write("\n".join(unfollow))

print(f"\n\033[96m[+] Liste {target}_unfollow.txt olarak kaydedildi!")
print("[+] jeawe_n tool’u kullandığın için sağol k
