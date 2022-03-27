<template>
  <div class="home">
    <input
      type="file"
      accept=".csv"
      class="file-input"
      @change="onFileChange"
    />
    <div class="testing">
      <div> {{ allDepositTotal }} </div>
      <div> {{ allWithdrawsTotal }} </div>
      <div v-if="profitTotal"> {{ profitTotal }} </div>
    </div>
  </div>
</template>

<script>
import * as Papa from 'papaparse';

export default {
  name: 'Home',
  data: () => ({
    csvData: [],
  }),
  computed: {
    /* eslint-disable */
    allDeposit() {
      if (this.csvData.length === 0) return;
      const onlyDeposit = this.csvData.filter((word) => word.typeOfOperation === 'ВВОД')
      // const newArray = onlyDeposit.map((s) => parseFloat(s.totalWithFee.replace(/,/g, ''))).reduce((a, b) => a + b)
      return onlyDeposit
    },
    allWithdraws() {
      if (this.csvData.length === 0) return;
      const onlyWithdraws = this.csvData.filter((word) => word.typeOfOperation === 'ВЫВОД')
      return onlyWithdraws
    },
    allDepositTotal: (vm) => vm.allDeposit?.map((s) => parseFloat(s.totalWithFee.replace(/,/g, ''))).reduce((a, b) => a + b),
    allWithdrawsTotal: (vm) => vm.allWithdraws?.map((s) => parseFloat(s.totalWithFee.replace(/,/g, ''))).reduce((a, b) => a + b),
    profitTotal: (vm) => vm.allWithdrawsTotal - vm.allDepositTotal,
  },
  methods: {
    onFileChange() {
      const fileInput = document.querySelector('.file-input');
      this.parseData(fileInput, this.setData, this.translateKey);
    },
    parseData(file, setData, translateKey) {
      Papa.parse(file.files[0], {
        header: true,
        encoding: 'ANSI',
        complete(results) {
          setData(results.data);
        },
        transformHeader(h) {
          return translateKey(h);
        },
      });
    },
    setData(data) {
      this.csvData = data;
    },
    translateKey(data) {
      if (data === 'Тип операции') return 'typeOfOperation';
      if (data === 'Дата') return 'date';
      if (data === 'Сумма перевода с учетом комиссии') return 'totalWithFee';
      if (data === 'Сумма перевода') return 'total';
      if (data === 'Комиссия') return 'fee';
      if (data === 'Способ оплаты') return 'typeOfPayment';
      if (data === 'Кошелек') return 'wallet';
      if (data === 'Идентификатор платежа') return 'paymentId';
      if (data === 'Плательщик') return 'payer';
      if (data === 'Получатель') return 'receiver';
      return 'user';
    },
  },
};
</script>
