<template>
  <div class="container">
    <h1>Quản lý sinh viên</h1>
    <button class="add-button" @click="toggleForm">Thêm mới sinh viên</button>

    <div style="display: flex">
      <table class="student-table">
        <thead>
          <tr>
            <th>Tên sinh viên</th>
            <th>Email</th>
            <th>Địa chỉ</th>
            <th>Số điện thoại</th>
            <th>Chức năng</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="student in students" :key="student.id">
            <td>{{ student.student_name }}</td>
            <td>{{ student.email }}</td>
            <td>{{ student.address }}</td>
            <td>{{ student.phone }}</td>
            <td>
              <button
                class="action-button edit"
                @click="editStudent(student.id)"
              >
                Sửa
              </button>
              <button
                class="action-button delete"
                @click="deleteStudent(student.id)"
              >
                Xóa
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <div v-if="isFormVisible" class="student-form">
        <h3 style="margin: 0; font-size: 18px">
          {{ currentStudentId ? "Cập nhật sinh viên" : "Thêm mới sinh viên" }}
        </h3>
        <form @submit.prevent="submitForm">
          <div class="form-group">
            <label for="">Tên sinh viên</label>
            <input
              v-model="newStudent.student_name"
              type="text"
              id="student_name"
              required
            />
          </div>
          <div class="form-group">
            <label for="">Email</label>
            <input v-model="newStudent.email" type="text" id="email" required />
          </div>
          <div class="form-group">
            <label for="">Số điện thoại</label>
            <input v-model="newStudent.phone" type="text" id="phone" required />
          </div>
          <div class="form-buttons">
            <button type="submit" class="save-button">
              {{ currentStudentId ? "Cập nhật" : "Thêm" }}
            </button>
            <button type="button" class="cancel-button" @click="toggleForm">
              Hủy
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";
const students = ref([]);
const isFormVisible = ref(false);
const currentStudentId = ref(null);

const newStudent = ref({
  student_name: "",
  email: "",
  address: "",
  phone: "",
  status: true,
  created_at: "",
});

const getAllStudent = async () => {
  const res = await axios.get("http://localhost:8080/users");
  students.value = res.data;
};

onMounted(() => {
  getAllStudent();
});

const toggleForm = () => {
  isFormVisible.value = !isFormVisible.value;
  if (!isFormVisible.value) {
    resetForm();
  }
};

const resetForm = () => {
  newStudent.value = {
    student_name: "",
    email: "",
    address: "",
    phone: "",
    status: true,
    created_at: "",
  };
};

const editStudent = (id) => {
  const student = students.value.find((s) => s.id === id);
  if (student) {
    newStudent.value = { ...student };
    currentStudentId.value = id;
    isFormVisible.value = true;
  }
};

const validateForm = () => {
  if (
    !newStudent.value.student_name ||
    !newStudent.value.email ||
    !newStudent.value.phone
  ) {
    alert("Vui lòng điền đầy đủ thông tin.");
    return false;
  }
  // Kiểm tra trùng email
  const isDuplicate = students.value.some(
    (student) => student.email === newStudent.value.email
  );
  if (isDuplicate) {
    alert("email đã tồn tại.");
    return false;
  }

  return true;
};

const submitForm = async () => {
  if (validateForm()) {
    if (currentStudentId.value) {
      // Nếu đang sửa sinh viên, thực hiện cập nhật
      const updatedStudent = {
        ...newStudent.value,
      };
      await axios.patch(
        `http://localhost:8080/users/${currentStudentId.value}`,
        updatedStudent
      );
      const index = students.value.findIndex(
        (s) => s.id === currentStudentId.value
      );
      if (index !== -1) {
        students.value[index] = updatedStudent;
      }
      toggleForm();
    } else {
      // Thêm sinh viên mới
      const createStudent = {
        ...newStudent.value,
      };
      await axios.post("http://localhost:8080/users", createStudent);
      toggleForm();
      getAllStudent();
    }
  }
};

const deleteStudent = async (id) => {
  const isConfirmed = window.confirm(
    "Bạn có chắc chắn muốn xóa sinh viên này không?"
  );
  if (isConfirmed) {
    await axios.delete(`http://localhost:8080/users/${id}`);
    alert("Xóa sinh viên thành công!");
    getAllStudent();
  }
};
</script>

<style>
.container {
  font-family: Arial, sans-serif;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}
.add-button,
.save-button,
.cancel-button {
  background-color: #4285f4;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  margin-bottom: 20px;
}
.cancel-button {
  background-color: #ff5252;
  margin-left: 10px;
}
.student-form {
  padding: 20px;
  border-radius: 8px;
}
.form-group {
  display: flex;
  flex-direction: column;
  margin: 10px 0;
}
.student-table {
  width: 100%;
  border-collapse: collapse;
}
.student-table th,
.student-table td {
  border: 1px solid #e0e0e0;
  padding: 12px;
  text-align: center;
}

.action-button {
  padding: 6px 12px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  margin-right: 5px;
}
.action-button.edit {
  background-color: #ffa000;
  color: white;
}
.action-button.delete {
  background-color: #ff5252;
  color: white;
}
</style>
