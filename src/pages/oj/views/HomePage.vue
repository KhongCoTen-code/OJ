<template>
  <div class="homepage-container">
    <div class="intro-section">
      <h1>Chào mừng đến với OJ!</h1>
      <p>
        OJ (Online Judge) là nền tảng luyện tập, thi đấu thuật toán và lập trình trực tuyến. Bạn có thể giải các bài tập, tham gia kỳ thi, kiểm tra thứ hạng và phát triển kỹ năng lập trình của mình.
      </p>
      <ul>
        <li>Kho bài tập đa dạng, cập nhật liên tục</li>
        <li>Chấm bài tự động, kết quả nhanh chóng</li>
        <li>Tham gia kỳ thi, xếp hạng cùng bạn bè</li>
        <li>Giao diện thân thiện, hỗ trợ đa ngôn ngữ</li>
      </ul>
      <Button type="primary" size="large" class="start-btn" @click="goToMain">Bắt đầu ngay</Button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomePage',
  methods: {
    goToMain() {
      this.$router.push({ name: 'problem-list' }) // hoặc route chính của bạn
    }
  }
}
</script>

<style scoped>
.homepage-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 80vh;
  background: #f7fafd;
}
.intro-section {
  background: #fff;
  padding: 40px 32px;
  border-radius: 12px;
  box-shadow: 0 2px 16px rgba(0,0,0,0.07);
  max-width: 500px;
  text-align: center;
}
.intro-section h1 {
  color: #0091CE;
  margin-bottom: 18px;
}
.intro-section ul {
  text-align: left;
  margin: 18px 0 28px 0;
  padding-left: 20px;
}
.start-btn {
  margin-top: 10px;
  font-size: 18px;
  padding: 8px 32px;
}
</style>
