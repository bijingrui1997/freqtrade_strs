# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 代码库概述

这是一个Freqtrade加密货币交易策略代码库，包含多种自动化交易策略的实现。主要专注于币安交易所的现货和合约交易。

### 核心组件

- **策略文件**: 位于各个子目录中的`.py`文件，继承自`IStrategy`接口
- **配置文件**: 各种`config.json`文件用于不同的交易环境（现货、合约、回测、实盘）
- **超参数优化**: `hyperopts_loss_function/`目录包含自定义的损失函数
- **Docker支持**: 使用Docker容器运行Freqtrade机器人

## 常用命令

### Freqtrade命令结构
所有Freqtrade命令都应该在对应的策略目录中执行，使用相应的配置文件：

```bash
# 回测
freqtrade backtesting --config config.json --strategy StrategyName --timerange YYYYMMDD-YYYYMMDD

# 超参数优化 - 买入信号
freqtrade hyperopt --config config.json --hyperopt-loss SortinoHyperOptLossDaily --strategy StrategyName --spaces buy

# 超参数优化 - 卖出信号  
freqtrade hyperopt --config config.json --hyperopt-loss SortinoHyperOptLoss --strategy StrategyName --spaces sell

# 模拟交易
freqtrade trade --config config.json --strategy StrategyName

# 实盘交易
freqtrade trade --config config.json --strategy StrategyName
```

### Docker命令
```bash
# 启动容器
docker-compose up -d

# 进入容器
docker-compose exec freqtrade bash

# 在容器中运行Freqtrade命令
docker-compose run --rm freqtrade [freqtrade命令]
```

## 项目架构

### 目录结构说明

- `/ClucHAnix/`: ClucHAnix策略的实现，支持不同时间周期
- `/binance/`: 币安交易所相关的所有文件
  - `/Archive/`: 历史策略文件归档
  - `/backtest&hyper_config/`: 回测和超参数优化配置
  - `/dry&live_config/`: 模拟和实盘交易配置
  - `/dry_run/buy1_and_buynew/`: 当前使用的E0V1E策略
  - `/hyperopts_loss_function/`: 自定义损失函数
  - `/personal/`: 个人Docker配置

### 当前主力策略

主要策略：`E0V1E.py` (位于`binance/dry_run/buy1_and_buynew/`)
- 使用5分钟时间周期
- 支持参数优化
- 包含买入和卖出信号逻辑
- 使用追踪止损

### 配置文件说明

不同环境使用不同配置：
- `config_binance_spot.json`: 现货交易配置
- `config_binance_future.json`: 合约交易配置
- `config_future.json`: 合约实盘配置
- `config_usdt_spot.json`: USDT现货配置

### 超参数优化

推荐的损失函数：
- 买入信号优化: `SortinoHyperOptLossDaily`
- 卖出信号优化: `SortinoHyperOptLoss`

自定义损失函数包括:
- `ExpectancyHyperOptLoss`
- `WinHyperOptLoss` 
- `MedianProfitHyperOptLoss`
- `QuickProfitHyperOptLoss`

## 开发注意事项

- 策略文件必须继承`IStrategy`类
- 配置文件中的API密钥字段为空，需要在实际使用时填入
- 黑名单配置排除了主流币种以专注于山寨币交易
- 使用VolumePairList按交易量选择交易对
- Telegram通知默认配置为静默模式