<script>
export default {
  onLaunch: function () {
    console.log('App Launch')
  },
  onShow: function () {
    console.log('App Show')
  },
  onHide: function () {
    console.log('App Hide')
  },
}
</script>

<style>
/* 全局主题（参考多邻国配色） */
:root{
  --duo-primary:#58cc02; /* 主绿色 */
  --duo-primary-dark:#4cb000;
  --duo-surface:#ffffff;
  --duo-border:#e5e7eb;
  --duo-text:#111111;
  --duo-muted:#6b7280;
  --duo-shadow:0 1px 2px rgba(0,0,0,.06);
  --duo-radius:12px;
}

page, body, .uni-page-body{
  background:var(--duo-surface);
  color:var(--duo-text);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'Noto Sans CJK SC', sans-serif;
}

button{
  border-radius:10px;
}

/* 每个页面公共css */
</style>
