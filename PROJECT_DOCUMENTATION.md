# 链眼 (ChainEye) - 链上风险检测工具

## 项目概述

**链眼** 是一个创新的链上钱包和代币风险检测工具，采用电锯人黑白极简风格和 JOJO 夸张字体设计，通过 AVE Cloud Data API 为用户提供**一眼看透链上风险**的体验。

### 核心特性

- 🎯 **即时风险评分**：输入钱包地址或代币合约，获得 0-100 分的健康评分
- 🎨 **JOJO 风格展示**：夸张的大字体和黑白高对比度设计，视觉冲击力强
- 🛡️ **全面风险检测**：蜜罐检测、买卖税分析、持仓集中度警告等
- 💬 **玛奇玛口吻文案**：冷静、掌控、温柔威慑的交互体验
- 🔗 **多链支持**：BSC、ETH、Base、Solana 等主流公链
- 📊 **详细分析报告**：代币详情、风险因素、建议和警告

---

## 技术架构

### 前端技术栈
- **框架**：React 19 + TypeScript
- **样式**：Tailwind CSS 4（OKLCH 色彩空间）
- **路由**：Wouter
- **UI 组件**：shadcn/ui + 自定义组件
- **字体**：Courier Prime（JOJO 风格）

### 后端技术栈
- **框架**：Express 4 + Node.js
- **API**：tRPC 11（类型安全的 RPC）
- **数据库**：MySQL/TiDB（可选）
- **测试**：Vitest

### 集成服务
- **AVE Cloud Data API**：链上数据查询和风险分析
- **Manus 平台**：项目托管和部署

---

## 使用的 AVE Skill

### 主要 Skill：`ave-data-rest`

**功能说明**：
- **Token Info**：获取代币基本信息（名称、符号、小数位、总供应量等）
- **Token Risk Report**：获取代币风险评估报告（蜜罐检测、税收分析等）
- **Wallet Holdings**：获取钱包持仓代币列表
- **Holder Distribution**：获取代币持仓者分布和集中度数据

**API 调用示例**：
```typescript
// 获取代币风险报告
const riskReport = await aveApi.getTokenRiskReport({
  tokenAddress: "0xfb62b9256eB641914501c81E58870293191CF471",
  chain: "bsc"
});

// 获取钱包持仓
const holdings = await aveApi.getWalletHoldings({
  walletAddress: "0x...",
  chain: "bsc"
});
```

---

## 功能演示

### 1. 主页搜索
- 居中搜索框设计，支持输入钱包地址或代币合约
- 链选择下拉菜单（BSC、ETH、Base、Solana）
- 快速示例按钮（PEPE、BNLife、DOGE、FLOKI、RUG、随机代币）

### 2. 风险报告页面
- **JOJO 风格大分数**：0-100 分的健康评分，采用夸张字体展示
- **风险等级标签**：SAFE（✅）、LOW、MEDIUM、HIGH、CRITICAL（⛔）
- **详细风险因素**：
  - 买卖税分析
  - 蜜罐检测
  - 持仓集中度
  - 流动性评估
  - 代币年龄
  - Mint/Burn 权限

### 3. 代币详情卡片
- 代币基本信息（名称、符号、地址）
- 价格和市值数据
- 流动性和交易对信息
- Top 持仓者分布
- AVE Pro 深度链接

### 4. 建议和警告
- 基于风险等级的个性化建议
- 玛奇玛口吻的冷静警告文案
- 可操作的风险缓解建议

---

## 风险评分算法

### 评分维度

| 维度 | 权重 | 说明 |
| :--- | :--- | :--- |
| 买卖税 | 20% | 买入/卖出税收比例 |
| 蜜罐风险 | 25% | 是否为蜜罐币 |
| 持仓集中度 | 20% | Top 持仓者占比 |
| 流动性 | 15% | 流动性池规模和比例 |
| 代币年龄 | 10% | 代币上线时间 |
| 权限风险 | 10% | Mint/Burn 权限 |

### 等级划分

- **SAFE (80-100)**：安全，可放心交易
- **LOW (60-79)**：低风险，注意但可接受
- **MEDIUM (40-59)**：中等风险，需谨慎
- **HIGH (20-39)**：高风险，强烈不建议
- **CRITICAL (0-19)**：严重风险，避免交易

---

## 项目结构

```
dominion-eye-ave-skill/
├── client/                    # 前端应用
│   ├── src/
│   │   ├── pages/            # 页面组件
│   │   │   ├── Home.tsx      # 主页（搜索框）
│   │   │   └── Report.tsx    # 风险报告页面
│   │   ├── components/       # 可复用组件
│   │   ├── lib/
│   │   │   └── makima-copy.ts # 玛奇玛口吻文案系统
│   │   ├── App.tsx           # 路由配置
│   │   └── index.css         # 全局样式
│   └── index.html
├── server/                    # 后端应用
│   ├── routers/
│   │   └── analysis.ts       # 分析路由
│   ├── risk-analyzer.ts      # 风险检测算法
│   ├── mock-data.ts          # 模拟数据生成器
│   ├── ave-api.ts            # AVE API 调用模块
│   ├── routers.ts            # tRPC 主路由
│   └── db.ts                 # 数据库查询
├── drizzle/                   # 数据库 schema
├── todo.md                    # 项目 TODO
└── package.json
```

---

## 核心代码示例

### 风险分析算法
```typescript
export function analyzeTokenRisk(factors: TokenRiskFactors): RiskReport {
  let score = 100;
  const riskFactors: RiskFactor[] = [];

  // 买卖税分析（权重 20%）
  if (factors.sellTax > 10) {
    score -= Math.min(factors.sellTax / 5, 20);
    riskFactors.push({
      name: "卖出税",
      severity: factors.sellTax > 50 ? "CRITICAL" : "HIGH",
      description: `卖出税 ${factors.sellTax}%，可能无法正常卖出`
    });
  }

  // 蜜罐检测（权重 25%）
  if (factors.isHoneypot) {
    score -= 25;
    riskFactors.push({
      name: "蜜罐风险",
      severity: "CRITICAL",
      description: "检测到蜜罐特征，资金可能被锁定"
    });
  }

  // ... 更多风险因子

  return {
    score: Math.max(0, Math.min(100, score)),
    level: getLevelFromScore(score),
    factors: riskFactors,
    warnings: generateWarnings(riskFactors),
    recommendations: generateRecommendations(riskFactors)
  };
}
```

### tRPC 路由示例
```typescript
export const analysisRouter = router({
  searchTokens: publicProcedure
    .input(z.object({
      query: z.string(),
      chain: z.string()
    }))
    .query(async ({ input }) => {
      const mockData = generateMockTokenData(input.query, input.chain);
      return mockData;
    }),

  getTokenRiskReport: publicProcedure
    .input(z.object({
      tokenAddress: z.string(),
      chain: z.string()
    }))
    .query(async ({ input }) => {
      const tokenData = getMockTokenData(input.tokenAddress);
      const riskReport = analyzeTokenRisk(tokenData.riskFactors);
      return riskReport;
    })
});
```

---

## 设计亮点

### 1. 电锯人黑白风格
- 纯黑白配色（OKLCH 色彩空间）
- 高对比度设计，易于阅读
- 粗犷线条感和阴影效果

### 2. JOJO 夸张字体
- 使用 Courier Prime 字体
- 大号字体显示评分（如 85）
- 加粗和大写字母强调重点

### 3. 玛奇玛口吻文案
- 冷静、掌控的语气
- 温柔中带有威慑感
- 例如："我掌握着你的一切风险"

### 4. 用户体验
- 搜索框居中，视觉焦点清晰
- 快速示例按钮，降低使用门槛
- 详细的风险分析，帮助用户做出决策

---

## 单元测试

项目包含完整的单元测试套件（10 个测试全部通过）：

### 风险分析测试
- ✅ 低风险代币评分测试
- ✅ 高风险代币评分测试
- ✅ 卖出税检测测试
- ✅ 持仓集中度检测测试
- ✅ 代币年龄检测测试
- ✅ 建议生成测试
- ✅ 风险等级信息测试

### 认证测试
- ✅ 登出功能测试

---

## 部署和使用

### 本地开发
```bash
# 安装依赖
pnpm install

# 启动开发服务器
pnpm dev

# 运行测试
pnpm test

# 构建生产版本
pnpm build
```

### 环境变量
```env
VITE_APP_ID=<Manus OAuth App ID>
OAUTH_SERVER_URL=https://api.manus.im
VITE_OAUTH_PORTAL_URL=<Manus OAuth Portal>
DATABASE_URL=<MySQL/TiDB Connection String>
JWT_SECRET=<Session Secret>
AVE_SKILL_API_KEY=<AVE API Key>
```

---

## 后期优化方向

1. **集成真实 AVE API**：当前使用模拟数据，可集成真实 AVE Cloud Data API
2. **钱包连接**：支持 MetaMask、WalletConnect 等钱包连接
3. **历史数据对比**：显示代币风险随时间的变化趋势
4. **用户收藏和提醒**：保存关注的代币并设置风险提醒
5. **更多链支持**：扩展到 Polygon、Arbitrum、Optimism 等
6. **深度学习预测**：使用 ML 模型预测代币未来风险

---

## 黑客松提交信息

| 项目 | 内容 |
| :--- | :--- |
| **项目名称** | 链眼 (ChainEye) |
| **AVE Skill 类型** | 监控类 Skills (ave-data-rest) |
| **项目格式** | Web 应用 |
| **源代码** | [GitHub 链接] |
| **文档** | 本文档 (PDF) |
| **演示视频** | [≤5 分钟演示视频链接] |

---

## 联系方式

- **项目主页**：https://chaineyeai.manus.space
- **GitHub**：[项目仓库链接]
- **文档**：[完整文档链接]

---

**最后更新**：2026 年 4 月 5 日  
**版本**：1.0.0  
**状态**：黑客松提交版本 ✅
