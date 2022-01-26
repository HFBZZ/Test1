<template>

  <router-view/>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.white-radius {
  background: white;
  border-radius: 10px;
  overflow: hidden;
  padding: 0.7rem;
  margin-bottom: 0.7rem;
}
/* 单行省略号*/
.one-hide {
  display: -webkit-box;
  overflow: hidden;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 1;
}
.three-hide {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;
}

.cls-con {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
}
.cls-flex {
  display: flex;
  align-items: center;
}
</style>
