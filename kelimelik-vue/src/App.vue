<template>
  <div id="app" class="container">
    <div class="card mt-4">
      <div class="card-header">soru</div>
      <div class="card-body">
        <div class="harf shadow">
          <span>X</span>
        </div>
      </div>
      <div class="card-footer">işlemler</div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  components: {
    
  }
}
</script>

<style>
  .harf{
    width: 100px;
    height: 100px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 30px;
  }
</style>
