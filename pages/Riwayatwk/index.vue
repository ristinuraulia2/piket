<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-lg-12">
        <h2 class="text-center my-4">Riwayat Piket</h2>
        <div class="my-3">
          <form @submit.prevent="getPiket">
            <div class="my-3">
              <input type="search" class="form-control form-control-lg rounded-5" placeholder="Cari hari..."
                v-model="keyword" <!-- Menghubungkan input dengan data keyword -->
            </div>
            <div class="my-3 text-muted">Menampilkan {{ visitors.length }} dari {{ totalData }}</div>
          </form>
        </div>
        <table class="table">
          <thead>
            <tr>
              <td>No</td>
              <td>Hari</td>
              <td>Tanggal</td>
              <td>Nama</td>
              <td>Tugas Piket</td>
              <td>Melaksanakan</td>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(visitor, i) in visitors" :key="i">
              <td>{{ i + 1 }}</td>
              <td>{{ visitor.Siswa?.jadwal_piket?.hari }}</td>
              <td>{{ visitor.tanggal }}</td>
              <td>{{ visitor.Siswa?.nama }}</td>
              <td>{{ visitor.Siswa?.tugas_piket?.nama || 'menyapu' }}</td>
              <td>{{ visitor.melaksanakan === null ? '' : (visitor.melaksanakan === true ? 'Ya' : 'Tidak') }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <NuxtLink to="/siswa/">
        <button type="button" class="btn btn-success">Kembali</button>
      </NuxtLink>
    </div>
  </div>
</template>

<style scoped>
table,
th,
td {
  border: 1px solid black;
  border-collapse: collapse;
}
</style>

<script setup>
import { ref, onMounted } from 'vue'
const supabase = useSupabaseClient()
const visitors = ref([])
const totalData = ref(0)
const keyword = ref('')

const getPiket = async () => {
  const { data, error, count } = await supabase
    .from('Piket')
    .select(`
      *,
      Siswa (
        nama,
        jadwal_piket( hari ), 
        tugas_piket( nama )
      )
    `, { count: 'exact' })
    .ilike('Siswa.jadwal_piket.hari', `%${keyword.value}%`) // Filter berdasarkan hari

  if (error) throw error
  if (data) {
    visitors.value = data
    totalData.value = count || 0 // Menyimpan total data yang diperoleh dari filter
  }
}

onMounted(() => {
  getPiket()
})
</script>
