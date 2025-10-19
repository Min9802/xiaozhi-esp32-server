# get_news_from_newsnow Plugin News Source Configuration Guide

## Overview

The `get_news_from_newsnow` plugin now supports dynamic configuration of news sources through the Web management interface, eliminating the need to modify code. Users can configure different news sources for each agent in the Smart Control Panel.

## Configuration Method

### 1. Configure Through Web Management Interface (Recommended)

1. Log in to Smart Control Panel
2. Enter the "Role Configuration" page
3. Select the agent to configure
4. Click the "Edit Functions" button
5. Find the "newsnow News Aggregation" plugin in the right parameter configuration area
6. Enter semicolon-separated Chinese names in the "News Source Configuration" field

### 2. Configuration File Method

Configure in `config.yaml`:

```yaml
plugins:
  get_news_from_newsnow:
    url: "https://newsnow.busiyi.world/api/s?id="
    news_sources: "ThePaper;Baidu Hot Search;Cailianshe;Weibo;Douyin"
```

## News Source Configuration Format

News source configuration uses semicolon-separated Chinese names with the format:

```
Chinese_Name1;Chinese_Name2;Chinese_Name3
```

### Configuration Example

```
ThePaper;Baidu Hot Search;Cailianshe;Weibo;Douyin;Zhihu;36Kr
```

## Supported News Sources

The plugin supports the following Chinese news source names:

- ThePaper (澎湃新闻)
- Baidu Hot Search (百度热搜)
- Cailianshe (财联社)
- Weibo (微博)
- Douyin (抖音)
- Zhihu (知乎)
- 36Kr (36氪)
- Wall Street Journal CN (华尔街见闻)
- IT Home (IT之家)
- Toutiao (今日头条)
- Hupuquan (虎扑)
- Bilibili (哔哩哔哩)
- Kuaishou (快手)
- Xueqiu (雪球)
- Gelunhui (格隆汇)
- Fabu Finance (法布财经)
- Jin10 Data (金十数据)
- Nowcoder (牛客)
- Sspai (少数派)
- Juejin (稀土掘金)
- Phoenix Net (凤凰网)
- Chongbuluo (虫部落)
- Zaobao (联合早报)
- Coolapk (酷安)
- CleanSky Forum (远景论坛)
- Reference News (参考消息)
- Sputnik News (卫星通讯社)
- Baidu Tieba (百度贴吧)
- Reliable News (靠谱新闻)
- And many more...

## Default Configuration

If no news sources are configured, the plugin will use the following default configuration:

```
ThePaper;Baidu Hot Search;Cailianshe
```

## Usage Instructions

1. **Configure News Sources**: Set the Chinese names of news sources in the Web interface or configuration file, separated by semicolons
2. **Call Plugin**: Users can say "broadcast news" or "get news"
3. **Specify News Source**: Users can say "broadcast ThePaper news" or "get Baidu Hot Search"
4. **Get Details**: Users can say "detailed introduction of this news"

## How It Works

1. The plugin accepts Chinese names as parameters (e.g., "ThePaper")
2. Based on the configured news source list, converts Chinese names to corresponding English IDs (e.g., "thepaper")
3. Uses English IDs to call API and get news data
4. Returns news content to users

## Important Notes

1. The configured Chinese names must match exactly with the names defined in CHANNEL_MAP
2. Service restart or configuration reload is required after configuration changes
3. If configured news sources are invalid, the plugin will automatically use default news sources
4. Use English semicolons (;) to separate multiple news sources, do not use Chinese semicolons (；)