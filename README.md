# Human–AI Transparency Study · 实验网页（GitHub Pages 部署版）

PSP404TC Applied Research Techniques and Methodologies · Lab5R
单文件网页，无需服务器、无需构建。被试完成后数据自动发到研究者邮箱。

---

## 一、文件说明

| 文件 | 作用 |
|---|---|
| `index.html` | 整个实验（欢迎/同意/练习/3 任务×2 条件/量表/问卷/结束页），**上传这一个就够** |
| `.nojekyll` | 空文件，告诉 GitHub Pages 别跑 Jekyll，避免文件被忽略 |
| `tools/merge_submissions.py` | 把后台导出的提交记录合并成一份可分析的 CSV |
| `screenshots/` | 各页面截图，报告 appendix 用（不必上传） |
| `sample-export.csv` | 导出文件的格式示例（不必上传） |

---

## 二、三步上线

### 1. 建仓库

登录 GitHub → 右上角 **+** → **New repository**

- Repository name：`aitp-study`（随便起，会成为链接的一部分）
- 选 **Public**（免费版 Pages 只支持公开仓库）
- 勾 **Add a README file** → **Create repository**

### 2. 上传网页

仓库页面 → **Add file** → **Upload files** → 把 `index.html` 和 `.nojekyll` 拖进去 → **Commit changes**

> 想在网页里直接改回传设置也可以：点开 `index.html` → 右上角铅笔图标 → 改完 Commit。

### 3. 打开 Pages

仓库 **Settings** → 左侧 **Pages** → Source 选 **Deploy from a branch**
→ Branch 选 **main**，目录选 **/ (root)** → **Save**

等 1–2 分钟刷新，页面顶部会出现绿色提示，链接形如：

```
https://你的用户名.github.io/aitp-study/
```

这就是发给同学的链接。手机扫码也能打开（页面已适配 390px 宽度）。

---

## 三、让数据自动进邮箱（关键一步）

GitHub Pages 只托管静态文件，发不了邮件，所以要借一个「表单转发服务」当代邮局——页面把数据 POST 过去，服务再转发到你邮箱。

### 方案 A · Web3Forms（推荐）

免费额度 250 次提交/月，**后台可以直接导出全部提交记录为 CSV**，这正是我们要的。

1. 打开 <https://web3forms.com> ，填邮箱 → 提交
2. 去邮箱点确认链接，拿到 **Access Key**（一长串字母数字）
3. 打开 `index.html`，在文件开头（搜 `var SUB`）找到这段：

```js
var SUB = {
  provider: "web3forms",   // "web3forms" | "formsubmit" | "none"
  web3formsKey: "",        // ← 填这里
  formsubmitTo: "",
  autoRetry: 2
};
```

4. 把 Key 粘进 `web3formsKey` 的引号里 → 保存 → 重新上传覆盖 `index.html`

### 方案 B · FormSubmit（不想注册时用）

免费、不限量，但**没有后台导出**，只能一封封收邮件，后期合并麻烦。

1. 把邮箱填进 `formsubmitTo`，`provider` 改成 `"formsubmit"`
2. 部署后自己完整做一遍实验，邮箱会收到一封**激活邮件**，点确认
3. 之后邮箱若收到一串随机别名（形如 `xxxxx@formsubmit.co`），用它替换真实邮箱，可避免地址暴露在网页源码里

> 两个都不填 = 纯离线模式：只保留「下载 CSV」按钮，行为和之前的本地版一致。

---

## 四、上线前必须自测（10 分钟）

1. 手机 + 电脑各打开一次链接，确认都能进、能切中英文
2. **完整做完一遍**（不要中途退出），看结束页的状态卡
   - 绿色「已提交成功」→ 成功
   - 橙色「自动提交没有成功」→ 网络或 Key 有问题，先修好再上线
3. 去邮箱 / Web3Forms 后台确认收到那条记录
4. 后台导出 CSV，跑一次合并脚本，确认 54 行数据完整：

```bash
python3 tools/merge_submissions.py downloads/submissions.csv combined.csv
```

---

## 五、现场操作（11.06）

1. 把链接 + 二维码发到班级群（二维码用草料二维码等工具把链接转一下即可）
2. 提醒：**不要中途退出**，否则数据到不了（页面会弹确认框拦一下）
3. 做完的人举手示意，实验者看一眼结束页是不是绿色对勾
4. 如果某人那台电脑提交失败：让他点「下载我的数据（CSV）」，微信发给你，一样的
5. 全部结束后，打开 Web3Forms 后台 → Export to CSV → 跑合并脚本

---

## 六、注意事项

**GitHub Pages 在国内访问可能很慢甚至打不开。** 校园网 / 手机热点环境下建议提前一天让两三个同学实测；如果确实打不开，把 `index.html` 原封不动传到下面任一平台，效果完全一样：

- Netlify（拖整个文件夹到 <https://app.netlify.com/drop>，秒上线）
- Vercel、Cloudflare Pages
- 腾讯云 COS / 阿里云 OSS 静态网站托管

**隐私。** 数据经由 Web3Forms（AWS 美国节点）转发，属于第三方处理。当前页面收集的是虚构材料下的任务反应，不含敏感信息，知情同意书里已经写明「通过 HTTPS 加密发送到研究者私人账户」。如果你们的伦理审查更严格，可以换成学校提供的表单服务，或者直接用离线版 + 现场收 CSV。

**Key 是公开的。** Web3Forms 的 Access Key 写在网页里，任何人查看源码都能看到。官方说它只相当于邮箱别名、不是密钥；但理论上有人可以刷你的表单。免费额度 250 次/月，一次实验 20 人，影响很小。

---

## 七、数据结构

每次提交包含：

| 字段 | 内容 |
|---|---|
| `session_id` | 随机编号，如 `P01-B2C` |
| `participant_name` / `student_id` | 选填，留空则为空 |
| `language` | `zh` / `en` |
| `block_order` | 实际任务顺序，如 `C:cue > C:baseline > A:baseline ...` |
| `n_trials` / `n_likert` / `n_survey` | 24 / 18 / 5 |
| `csv` | 该被试的完整 CSV 文本 |
| `csv_b64` | 同一份 CSV 的 Base64，合并脚本优先用它（不会被邮件格式破坏） |

CSV 表头：`participant_id, record_type, block, task, hypothesis, condition, condition_label, item, truth, response, inspected, decision_ms, measure, value, text, elapsed_ms, language, timestamp`
