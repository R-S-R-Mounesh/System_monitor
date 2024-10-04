<template>
  <div class="side-menu">
    <h3 class="title">System Health Monitor</h3>
    <ul>
      <li><router-link to="/system-health">Dashboard</router-link></li>
      <li><router-link to="/">System Health</router-link></li>
      <li><router-link to="/system-list">Systems</router-link></li> <!-- Link to System List -->
      <li><a href="#">Logout</a></li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'SideMenu',
};
</script>

<style scoped>
.side-menu {
  width: 250px;
  background-color: #140244;
  color: white;
  padding: 20px;
  position: fixed;
  height: 100%;
  left: 0;
  top: 0;
}

.side-menu h2 {
  margin-top: 0;
}

.side-menu ul {
  list-style: none;
  padding: 0;
}

.side-menu li {
  margin: 15px 0;
}
.side-menu h3{
  font-weight: 500px;
}

.side-menu a {
  color: white;
  text-decoration: none;
}

.side-menu a:hover {
  text-decoration: underline;
}
</style>
