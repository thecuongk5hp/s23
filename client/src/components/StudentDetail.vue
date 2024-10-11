<template>
  <div>
    <h2>Chi tiết sinh viên</h2>
  </div>
</template>

<script setup>
import { onMounted } from "vue";
import axios from "axios";

const getStudentById = async (id) => {
  try {
    const res = await axios.get(`http://localhost:8080/users/${id}`);

    if (res.data) {
      console.log(res.data);
    } else {
      console.log("Không tìm thấy bản ghi");
    }
  } catch (error) {
    console.error("Lỗi khi gọi API:", error);
  }
};

onMounted(() => {
  const studentId = 5;
  getStudentById(studentId);
});
</script>

<style scoped>
h2 {
  color: #333;
}
</style>
