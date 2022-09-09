# dice-game
一个筛子游戏，思路来源于 "咩咩启示录" 里的小游戏。

## 玩法
> 游戏为两名玩家，每位玩家拥有一个 3X3 的棋盘
> 通过摆放数字形成总数，最终总数大的玩家获胜

- 同列相同的数字，相乘计算
- 同列不同的数字，相加计算
- 放置数字时，如果对方相同列拥有相同的数字，则全部消掉
- 任意一名玩家摆满棋盘后，视为游戏结束，计算总数

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
