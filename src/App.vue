<script setup>
  import { ref, onMounted, computed, watch } from "vue";

  const bills = ref([]);
  const name = ref("");

  const input_content = ref("");
  const input_price = ref("")
  const input_category = ref(null);

  const bills_asc = computed(() =>
    bills.value.sort((a, b) => {
      return b.createdAt - a.createdAt;
    })
  );

  const total_price = computed(()=>{
   return bills.value.reduce((sum, bill) => {
      return sum + bill.price;
    }, 0)
  })


  watch(
    bills,
    (newVal) => {
      localStorage.setItem("bills", JSON.stringify(newVal));
    },
    { deep: true }
  );

  watch(name, (newVal) => {
    localStorage.setItem("name", newVal);
  });




  const addBill = () => {
    if (input_content.value.trim === '' || input_category.value === null) {
      return;
    }

    bills.value.push({
      content: input_content.value,
      price: input_price.value,
      category: input_category.value,
      done: false,
      editable: false,
      createdAt: new Date().getTime()
    });

  };

  const removeBill = bill => {
      bills.value = bills.value.filter(t => t !== bill)
  }



  onMounted(() => {
    name.value = localStorage.getItem('name') || '';
    bills.value = JSON.parse(localStorage.getItem('bills')) || [];
  });
</script>

<template>
  <main>
    <section class="greeting">
      <h1 class="title">
        Olá, <input type="text" placeholder="Seu nome aqui" v-model="name" />
      </h1>
    </section>

    <section class="create-bill">

      <form id="new-bill-form" @submit.prevent="addBill">
        <h4>Quais contas pagar?</h4>
        <input
          type="text"
          placeholder="Exemplo: conta de luz"
          v-model="input_content"
        />

        <h4>Quail o valor?</h4>
        <input
          type="number"
          placeholder="Exemplo: 100"
          v-model="input_price"
        />

        <h4>A conta é sua ou da casa?</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Sua conta</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Conta casa</div>
          </label>

          <input type="submit" value="Adicionar conta" />
        </div>
      </form>
    </section>

    <section class="bill-list">
      <h3>Contas a Pagar</h3>
      <div class="list" id="bill-list">
        <div
          v-for="bill in bills_asc"
          :class="`bill-item ${bill.done && 'done'}`"
        >
        
            <label>
                <input type="checkbox" v-model="bill.done">
                <span :class="`bubble ${bill.category}`"></span>
            </label>

            <div class="bill-content">
                <input type="text" v-model="bill.content">
                <div>
                R$ <input type="number" v-model="bill.price" class="bill-price">
                </div>
            </div>


            <div class="actions">
                <button class="delete" @click="removeBill(bill)">Delete</button>
            </div>

            
        </div>
      </div>
    </section>
      Valor total : R${{ total_price }}
  </main>
</template>
