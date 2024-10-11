<template>
  <div>
    <h2>ListStudent</h2>
    <button @click="removeStudentById">Delete</button>
    <button @click="createStudent">Create Student</button>
    <button @click="updateStudentById">Update Product</button>
    <ul>
      <li v-for="student in students" :key="student.id">
        {{ student.student_name }} - {{ student.email }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";

const students = ref([]);

const getAllStudent = async () => {
  const res = await axios.get("http://localhost:8080/users");
  students.value = res.data;
};
onMounted(() => {
  getAllStudent();
});

const removeStudentById = async () => {
  const id = 14;
  await axios.delete(`http://localhost:8080/users/${id}`);
  getAllStudent();
};

const createStudent = async () => {
  const newStudent = {
    id: 14,
    student_name: "Tạ Thị O",
    email: "tathio@email.com",
    address: "1111 Đường Lý Tự Trọng, Thanh Hóa",
    phone: "0934567890",
    status: true,
    created_at: "2024-10-13",
  };

  await axios.post("http://localhost:8080/users", newStudent);
  getAllStudent();
};

const updateStudentById = async () => {
  const id = 14;
  const student = {
    id: 14,
    student_name: "Tạ Thị A",
    email: "tathia@email.com",
    address: "Hà Nội",
    phone: "0934567890",
    status: true,
    created_at: "2024-10-13",
  };

  await axios.patch(`http://localhost:8080/users/${id}`, student);
  getAllStudent();
};
</script>

<style></style>
