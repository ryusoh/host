This repo is a host of media files for my static website ecosystem.

The files are uploaded via [PicGo](https://picgo.github.io/PicGo-Doc/en/guide/#picgo-is-here).

Access: https://github.com/setting/tokens

Images are my own.

⸻

raw.githubusercontent.com sits on GitHub’s Fastly edge and is intermittently throttled or outright reset by the Great Firewall, so images can vanish for users in China.
The quickest “zero‑setup” fix is just rewrite the URL to a mirror that fronts GitHub through a China‑reachable CDN, e.g.

https://ghproxy.net/https://raw.githubusercontent.com/ryusoh/imagehost/refs/heads/master/DSCF0361-2.jpg

or

https://cdn.staticaly.com/gh/ryusoh/imagehost/master/DSCF0361-2.jpg

Below are the practical options, trade‑offs, and copy‑paste patterns.

⸻

1  Why the image breaks in China
	•	raw.githubusercontent.com has been on the GFW block‑list since at least 2019; RST/timeout rates spike unpredictably.  ￼
	•	The host sits on Fastly (AS54113), another CDN that lost its ICP filing; DNS poisoning and TCP resets are common.  ￼ ￼
	•	Result: high packet‑loss in most China probes, exactly what your ping table shows.

⸻

2  One‑liner URL mirrors (no account, no config)

Mirror	Pattern	CDN / Notes
ghproxy.net	https://ghproxy.net/ {raw‑URL}	Cloudflare edge; designed for China; very fast and stable.  ￼
raw.fastgit.org	Replace host with raw.fastgit.org	Also Cloudflare; free.  ￼ ￼
raw.iqiq.io	Replace host with raw.iqiq.io	iQZone mirror; simple rewrite.  ￼
Staticaly	`https://cdn.staticaly.com/gh/{user}/{repo}/{branch	tag}/{path}`
jsDelivr	https://cdn.jsdelivr.net/gh/{user}/{repo}@{branch}/{path}	Works if ISP hasn’t poisoned route; less reliable since ICP revoked.  ￼

Usage example (ghproxy):

<img src="https://ghproxy.net/https://raw.githubusercontent.com/ryusoh/imagehost/refs/heads/master/DSCF0361-2.jpg">

No new hosting, no login, just a URL swap.

⸻

3  Option: free but slightly more work—Cloudflare

3.1 Cloudflare R2 public bucket

Drag‑drop the file into an R2 bucket and enable “Public Bucket”; you get a https://<bucket>.r2.dev/<file> link.  ￼
Pros: global edge, signed‑URL option. Cons: some Chinese regions block r2.dev; use a custom domain on Cloudflare DNS for best reach.  ￼

3.2 Cloudflare Pages / Workers

Push the image to a Pages project or serve it from a Worker KV; no ICP if you keep Cloudflare’s domain.

⸻

4  Option: Mainland CDNs (fully compliant)

If you later need 100 % reliability inside China, migrate the image folder to a China CDN such as Tencent COS, Aliyun OSS, or Qiniu. Requires real‑name verification and an ICP record for custom domains but guarantees first‑class speed.  ￼

⸻

5  Best‑effort fallback in HTML

Add a graceful degradation:

<img src="https://ghproxy.net/https://raw.githubusercontent.com/ryusoh/imagehost/refs/heads/master/DSCF0361-2.jpg"
     onerror="this.src='https://cdn.staticaly.com/gh/ryusoh/imagehost/master/DSCF0361-2.jpg'">

If the first mirror fails, the browser pulls the second automatically.

⸻

✔ Recommendation

For “no extra fuss,” start with ghproxy.net (fast, Cloudflare, one‑liner).
If your audience grows or you need guarantees, move to a China ICP‑backed CDN later.
