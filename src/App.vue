<template>
  <div class="w-[80%] m-auto mt-4 h-[100vh]">
    <header class="d-flex justify-content-between mb-3">
      <h3>Nhân viên</h3>
      <button class="btn btn-primary" @click="openAddEmployeeForm">
        Thêm nhân viên
      </button>
    </header>

    <input
      type="text"
      class="form-control mb-3"
      v-model="searchEmail"
      placeholder="Tìm kiếm theo email"
    />

    <!-- Danh sách nhân viên -->
    <table class="table table-bordered">
      <thead>
        <tr>
          <th>STT</th>
          <th>Họ và tên</th>
          <th>Email</th>
          <th>Trạng thái</th>
          <th>Chức năng</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(employee, index) in filteredEmployees"
          :key="employee.email"
        >
          <td>{{ index + 1 }}</td>
          <td>{{ employee.name }}</td>
          <td>{{ employee.email }}</td>
          <td>
            {{
              employee.status === "active"
                ? "Đang hoạt động"
                : "Ngừng hoạt động"
            }}
          </td>
          <td>
            <button @click="toggleBlock(employee)" class="btn btn-secondary">
              {{ employee.status === "active" ? "Chặn" : "Bỏ chặn" }}
            </button>
            <button @click="editEmployee(employee)" class="btn btn-warning">
              Sửa
            </button>
            <button @click="confirmDelete(employee)" class="btn btn-danger">
              Xóa
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Form thêm mới nhân viên -->
    <div v-if="showAddForm" class="overlay">
      <form class="form">
        <h4>Thêm/Sửa nhân viên</h4>
        <div>
          <label>Họ và tên</label>
          <input v-model="formData.name" type="text" class="form-control" />
        </div>
        <div>
          <label>Email</label>
          <input v-model="formData.email" type="email" class="form-control" />
        </div>
        <button class="btn btn-primary mt-3 w-100" @click="addOrUpdateEmployee">
          Lưu
        </button>
      </form>
    </div>

    <!-- Modal xác nhận xóa -->
    <div v-if="showDeleteConfirm" class="overlay">
      <div class="modal-custom">
        <h4>Cảnh báo</h4>
        <p>Bạn có chắc chắn muốn xóa nhân viên này?</p>
        <button class="btn btn-light" @click="closeConfirm">Hủy</button>
        <button class="btn btn-danger" @click="deleteEmployee">Xóa</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

const employees = ref([
  { name: "Nguyễn Văn A", email: "nvana@gmail.com", status: "active" },
  { name: "Trần Thị B", email: "ttb@gmail.com", status: "inactive" },
  { name: "Lê Văn C", email: "lvc@gmail.com", status: "active" },
]);

const searchEmail = ref("");
const showAddForm = ref(false);
const showDeleteConfirm = ref(false);
const currentEmployee = ref(null);
const formData = ref({ name: "", email: "" });

const filteredEmployees = computed(() => {
  return employees.value.filter((e) =>
    e.email.toLowerCase().includes(searchEmail.value.toLowerCase())
  );
});

const openAddEmployeeForm = () => {
  showAddForm.value = true;
};

const closeForm = () => {
  showAddForm.value = false;
  formData.value = { name: "", email: "" };
};

const addOrUpdateEmployee = () => {
  if (currentEmployee.value) {
    Object.assign(currentEmployee.value, formData.value);
  } else {
    employees.value.push({ ...formData.value, status: "active" });
  }
  closeForm();
};

const editEmployee = (employee) => {
  formData.value = { ...employee };
  currentEmployee.value = employee;
  openAddEmployeeForm();
};

const confirmDelete = (employee) => {
  currentEmployee.value = employee;
  showDeleteConfirm.value = true;
};

const closeConfirm = () => {
  showDeleteConfirm.value = false;
};

const deleteEmployee = () => {
  employees.value = employees.value.filter(
    (e) => e.email !== currentEmployee.value.email
  );
  closeConfirm();
};

const toggleBlock = (employee) => {
  employee.status = employee.status === "active" ? "inactive" : "active";
};
</script>

<style>
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.5);
}

.form,
.modal-custom {
  background: white;
  padding: 20px;
  border-radius: 8px;
}

.form input,
.form button {
  margin-bottom: 10px;
}
</style>
