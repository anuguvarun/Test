get errorConfig() {
  return {
    required: this.isFi
      ? { msg: 'Please enter CUSIP.' }
      : { msg: 'Please enter symbol.' },

    searchPattern:
      this.search.value !== '' && this.search.value !== null
        ? this.isFi
          ? { msg: 'Please enter 9 alphanumeric characters.' }
          : { msg: 'Please enter from 2 to 5 alphanumeric characters.' }
        : { msg: '' },

    securityResponse:
      this.search.value !== '' && this.search.value !== null
        ? { msg: 'Security not found.' }
        : { msg: '' },

    securitySearchError:
      this.search.value !== '' &&
      this.search.value !== null &&
      this.searchOptions?.length === 0
        ? this.tradeType.value === 'MF'
          ? { msg: 'Please enter valid Mutual Fund symbol.' }
          : this.tradeType.value === 'ETF'
          ? { msg: 'Please enter valid ETF symbol.' }
          : { msg: 'Security not found.' }
        : { msg: '' },

    duplicate: this.isFi
      ? { msg: 'Duplicate entry, choose a different CUSIP.' }
      : { msg: 'Duplicate entry, choose a different symbol.' },
  };
}





export class StepCardComponent {
  isFi: boolean;
  search: { value: string | null };
  tradeType: { value: string };
  searchOptions: any[];

  get errorConfig() {
    return {
      required: this.isFi
        ? { msg: 'Please enter CUSIP.' }
        : { msg: 'Please enter symbol.' },

      searchPattern:
        this.search.value !== '' && this.search.value !== null
          ? this.isFi
            ? { msg: 'Please enter 9 alphanumeric characters.' }
            : { msg: 'Please enter from 2 to 5 alphanumeric characters.' }
          : { msg: '' },

      securityResponse:
        this.search.value !== '' && this.search.value !== null
          ? { msg: 'Security not found.' }
          : { msg: '' },

      securitySearchError:
        this.search.value !== '' &&
        this.search.value !== null &&
        this.searchOptions?.length === 0
          ? this.tradeType.value === 'MF'
            ? { msg: 'Please enter valid Mutual Fund symbol.' }
            : this.tradeType.value === 'ETF'
            ? { msg: 'Please enter valid ETF symbol.' }
            : { msg: 'Security not found.' }
          : { msg: '' },

      duplicate: this.isFi
        ? { msg: 'Duplicate entry, choose a different CUSIP.' }
        : { msg: 'Duplicate entry, choose a different symbol.' },
    };
  }
}
