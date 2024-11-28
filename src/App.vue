<template>
  <!--------- Form-Modal ----->
<div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-xl">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">{{ isEdit ? 'Update Main Category' : '+ Add New Main Category ' }}</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <form @submit.prevent="submitForm" class="main-form">
          <section style="border: 1px solid gray; padding: 2rem;">
          <div class="mb-3">
            <label for="recipient-name" class="col-form-label">Main Category Name <span style="color: red;">*</span></label>
            <input type="text" class="form-control" v-model="formMainCategory.nameEn" >
          </div>
          <div class="d-flex gap-3">
          <div class="mb-3 w-100">
            <label for="message-text" class="col-form-label">Main Category Code <span style="color: red;">*</span></label>
            <input type="text" class="form-control" v-model="formMainCategory.code" :disabled="isEdit">
          </div>
          <div class="mb-3 w-100">
            <label for="message-text" class="col-form-label">Main Category Name (Khmer) <span style="color: red;">*</span></label>
            <input type="text" class="form-control" v-model="formMainCategory.nameKh">
          </div>
        </div>
          <label for="message-text" class="col-form-label">+ Search category to add</label>
          <div>
            <input type="text" class="form-control" v-model="searchByName">
        <div style="margin-top: 2rem !important;">
          <table class="table table-striped">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Image</th>
          <th scope="col">#Category Code</th>
          <th scope="col">Category Name</th>
          <th scope="col">Department Name</th>
          <th scope="col">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(sub, index) in filteredSubcategories" :key="index" style="font-size: 0.9rem;">
          <td scope="row">{{ index + 1 }}</td>
          <td><img :src="sub.image" alt="image" style="width: 50px;"></td>
          <td>{{ sub.code }}</td>
          <td >{{ sub.nameEn }}</td>
          <td>{{ sub?.department?.nameEn }}</td>
          <td style="width: 0; text-align: center;">
            <i @click.prevent="deleteSubCategory(sub.code)" style="cursor: pointer;" class="bi bi-trash text-danger fs-4"></i>
          </td> 
        </tr>
      </tbody>
    </table>
        </div>
          </div>
          <p v-if="filteredSubcategories.length === 0" style="text-align: center;">Not Found!</p>
     </section>
        <!---------------- upload image --------->
    <section style="border: 1px solid gray; padding: 2rem; height: 50vh;">
  <div class="form-check">
    <label class="form-check-label" for="flexCheckDefault">
    Status
  </label>
     <input class="form-check-input" type="checkbox" value="" id="flexCheckDefault" v-model="formMainCategory.isActive">
   </div>
    <input type="file" class="form-control" @change="handleImageUpload" accept="image/*"  ref="fileImageInput">
    <div v-if="imagePreview">
        <img :src="imagePreview" class="mt-2" style="max-width: 100px;" alt="category-image">
    </div>
    <div class="mb-3 ">
        <label for="message-text" class="col-form-label">Display Order</label>
        <input type="text" class="form-control" v-model="formMainCategory.displayOrder">
          </div>
        </section>
        </form>
      </div>
      <div class="modal-footer">
        <button @click="submitForm()" data-bs-dismiss="modal" aria-label="Close" style="width: 100%;" type="button" class="btn btn-primary">Save</button>
      </div>
    </div>
  </div>
</div>

    <!------------ List Main Category ----------->
  <div class="container-fluid p-4">
    <div style="display: flex; justify-content: end; margin-bottom: 1.5rem;">
      <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal" data-bs-whatever="@mdo" style="background-color: #5da2ec; color: white;" @click="openModal()">Add New +</button>
    </div>
    <section class="filter">
      <div>
        <i class="bi bi-funnel fs-4"></i>
        Filters
      </div>
      <div class="filter-input">
        <input type="text" class="form-control" v-model="searchText" style="width: 300px;" placeholder="Search by name...">
        <select class="form-select" aria-label="Default select example" v-model="itemsPerPage" @change="changeItemsPerPage">
          <option value="5">Show 5</option>
          <option value="10">Show 10</option>
          <option value="20">Show 20</option>
          <option value="30">Show 30</option>
          <option value="40">Show 40</option>
        </select>
      </div>
    </section>

    <table class="table table-bordered">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Image</th>
          <th scope="col">#Category Code</th>
          <th scope="col">Category Name</th>
          <th scope="col">Category Name (Khmer)</th>
          <th scope="col">Display Order</th>
          <th scope="col">Status</th>
          <th scope="col">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(main, index) in paginatedMainCategory" :key="index">
          <td scope="row">{{ startIndex + index + 1 }}</td>
          <td><img :src="main.image" alt="image" style="width: 50px;"></td>
          <td>{{ main.code }}</td>
          <td>{{ main.nameEn }}</td>
          <td>{{ main.nameKh }}</td>
          <td>{{ main.displayOrder }}</td>
          <td><span :class="{ isactive : main.isActive, noActive : !main.isActive}">{{ main.isActive ? 'Active' : 'NoActive'}}</span></td>
          <td class="d-flex gap-1">
            <i data-bs-toggle="modal" data-bs-target="#exampleModal" data-bs-whatever="@mdo" @click="editCategory(main)" style="cursor: pointer; margin-left: 1rem;" class="bi bi-pencil-square text-primary fs-4"></i>
            <i @click.prevent="deleteCategory(main.code)" style="cursor: pointer;" class="bi bi-trash text-danger fs-4"></i>
          </td>
        </tr>
      </tbody>
    </table>
    <section class="pagination-section">
      <div>
        Showing {{ startIndex + 1 }} to {{ endIndex }} of {{ filteredRecords.length }} records
      </div>
      <p v-if="filteredRecords.length === 0">Category Not Found!</p>
      <nav aria-label="...">
        <ul class="pagination">
          <li class="page-item" :class="{ disabled: currentPage === 1 }">
            <a class="page-link" href="#" @click.prevent="previousPage">Previous</a>
          </li>
          <li class="page-item" v-for="page in totalPages" :key="page" :class="{ active: page === currentPage }">
            <a class="page-link" href="#" @click.prevent="goToPage(page)">{{ page }}</a>
          </li>
          <li class="page-item" :class="{ disabled: currentPage === totalPages }">
            <a class="page-link" href="#" @click.prevent="nextPage">Next</a>
          </li>
        </ul>
      </nav>
    </section>
  </div>
</template>

<script setup lang="ts">
import axios from 'axios';
import Swal from 'sweetalert2'
import { computed, onMounted, ref, watch } from 'vue';
import { MainCategory } from './Models/main-category';
import { subCategory } from './Models/main-category';

const Main_Category_Api = 'https://api-dev-supermarket.chipmongretail.com/api/v1/MainCategory';
const Category_Api = 'https://api-dev-supermarket.chipmongretail.com/api/v1/Category';
const authorizationToken = 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IjkwMDE5MjE3IiwianRpIjoiYmU4ODUwNzctOTQyMS00ODc5LWI3NDYtZDE1ZTg1MDgzZjk1IiwiYXVkIjpbImh0dHBzOi8vYXBpLWRldi1jb21tb24uY2hpcG1vbmdyZXRhaWwuY29tOjgwODEiLCJodHRwczovL2FwaS1kZXYtbWFsbC5jaGlwbW9uZ3JldGFpbC5jb20iLCJodHRwczovL2FwaS1kZXYtc3VwZXJtYXJrZXQuY2hpcG1vbmdyZXRhaWwuY29tIl0sIm5iZiI6MTczMjUxNTQ2NSwiZXhwIjoxNzMzMTIwMjY1LCJpYXQiOjE3MzI1MTU0NjUsImlzcyI6Imh0dHBzOi8vYXBpLWRldi1jb21tb24uY2hpcG1vbmdyZXRhaWwuY29tOjgwODEifQ.5f5H71nGt9IjrrymcEXDJDb-T_bBwdrL9oqraVbYtXQ';
const mainCategory = ref<MainCategory[]>([]);
const subcategories = ref<subCategory[]>([]);

const currentPage = ref(1);
const itemsPerPage = ref(5);
const totalRecords = ref(0);
const searchText = ref('');
const searchByName = ref('')
const isEdit = ref(false);

const formMainCategory = ref<MainCategory>({
  code: '',
  nameEn: '',
  nameKh: '',
  image: '',
  isActive: false,
  displayOrder: null,
});
const selectedFile = ref(null);
const imagePreview = ref('')
const openModal = () =>{
  isEdit.value = false;
  formMainCategory.value = {
  code: '',
  nameEn: '',
  nameKh: '',
  image: '',
  isActive: false,
  displayOrder: null,
  }
  imagePreview.value = '';  
  clearFilePhotoInput();
  subcategories.value = [];
}

//******** Logic for CRUD Main Category*********
const fetchMainCategory = async () => {
  try {
    const res = await axios.get(`${Main_Category_Api}/GetAll`, {
      headers: {
        Authorization: authorizationToken,
        'Content-Type': 'multipart/form-data'
      }
    });
    mainCategory.value = res.data.data;
    totalRecords.value = mainCategory.value.length;
  } catch (err) {
    console.log('fetch error', err);
  }
};

const submitForm = async () => {
  try {
    if (isEdit.value) {
      await axios.put(`${Main_Category_Api}/Update/${formMainCategory.value.code}`, formMainCategory.value, {
        headers: {
          Authorization: `${authorizationToken}`,
          'Content-Type': 'multipart/form-data' 
        } 
      });
    } else {
      await axios.post(`${Main_Category_Api}/Create`, formMainCategory.value, {
        headers: {
          Authorization: `${authorizationToken}`,
          'Content-Type': 'multipart/form-data'
        }
      });
    }
    fetchMainCategory();
  } catch (err) { 
    console.log('Error submitting form', err);
  }
};

const editCategory = (category: MainCategory) => {
  isEdit.value = true;
  formMainCategory.value = { ...category }; //... clone all filed from form
  imagePreview.value = category.image || '';
  fetchCategory(category.code);
};

const deleteCategory = async (code: string) =>{
  Swal.fire({
  title: "Are you sure?",
  text: "You won't be able to revert this!",
  icon: "warning",
  showCancelButton: true,
  confirmButtonColor: "#3085d6",
  cancelButtonColor: "#d33",
  confirmButtonText: "Yes, delete it!"
}).then(async(result) => {
  if (result.isConfirmed) {
    await axios.delete(`${Main_Category_Api}/Delete/${code}`, {
      headers: {
            Authorization: `${authorizationToken}`
      } 
    })
    Swal.fire({
      title: "Deleted!",
      text: "Your file has been deleted.",
      showConfirmButton: false,
      icon: "success",
      timer: 2000
    });
    fetchMainCategory();
  }
});
}

const fetchCategory = async (code: string) => {
  try {
    const res = await axios.get(`${Category_Api}/GetById/${code}`, {
      headers: {
        Authorization: authorizationToken,
        'Content-Type': 'multipart/form-data'
      }
    });
    if (res.data && res.data.subcategories) {
      subcategories.value = res.data.subcategories;
    } else {
      subcategories.value = [];
    }
  } catch (err) {
    console.log('fetch error', err);
  }
};

//filter category
const filteredSubcategories = computed(() => {
  return subcategories.value.filter((item: any) =>
    item.nameEn.toLowerCase().includes(searchByName.value.toLowerCase())
  );
});

const deleteSubCategory = async (code: string) =>{
    await axios.delete(`${Category_Api}/Delete/${code}`, {
      headers: {
            Authorization: `${authorizationToken}`
      } 
    })  
    fetchCategory(code);
}

//******** handle upload image, clear image and preview image when upload********
const handleImageUpload = (event: any) => {
      const file = event.target.files[0];
      if (file) {
        selectedFile.value = file;
        imagePreview.value = URL.createObjectURL(file);
        formMainCategory.value.image = file;
  }
}

const fileImageInput = ref<HTMLInputElement | null>(null);

const clearFilePhotoInput = () => {
  if (fileImageInput.value) {
    fileImageInput.value.value = '';
  }
  formMainCategory.value.image = '';
  imagePreview.value = ''; 
};

//******** Logic for filter and pagination*********
//filter mainCategory
const filteredRecords = computed(() => {
  return mainCategory.value.filter((item) =>
    item.nameEn.toLowerCase().includes(searchText.value.toLowerCase())
  );
});

const paginatedMainCategory = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  return filteredRecords.value.slice(start, end);
});

const totalPages = computed(() => {
  return Math.ceil(filteredRecords.value.length / itemsPerPage.value);
});

const startIndex = computed(() => {
  return (currentPage.value - 1) * itemsPerPage.value;
});

const endIndex = computed(() => {
  return Math.min(startIndex.value + itemsPerPage.value, filteredRecords.value.length);
});

const previousPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

const goToPage = (page: number) => {
  currentPage.value = page;
};

const changeItemsPerPage = () => {
  currentPage.value = 1;
};

watch(searchText, () => {
  currentPage.value = 1;
});

onMounted(() => {
  fetchMainCategory();
});

</script>

<style scoped>
.isactive {
  font-size: 0.8rem;
  color: white;
  background-color: rgb(71, 171, 71);
  padding: 0.3rem 0.7rem;
  border-radius: 1rem;
}

.noActive {
  font-size: 0.8rem;
  color: white;
  background-color: rgb(224, 79, 103);
  padding: 0.3rem 0.7rem;
  border-radius: 1rem;
}

th {
  background-color: #5da2ec !important;
  color: white !important;
}

.filter {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  border: 1px solid rgb(165, 165, 165);
  padding: 1rem;
  border-bottom: none;
}

.filter-input {
  display: flex;
  gap: 1rem;
}

.pagination-section {
  display: flex;
  justify-content: space-between;
  margin-top: 2rem;
}

.main-form{
  display: flex;
 justify-content: space-between;
 padding: 1rem;
 flex-wrap: wrap;
}
@media only screen and (max-width: 628px) {
  .filter-input {
    margin-top: 1rem;
  }
}
</style>
