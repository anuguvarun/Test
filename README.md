export class SearchComponent {
  isFi: boolean = true; // or however it's set
  search = { value: '' };
  tradeType = { value: '' };
  tradeTypeEnum = 'someValue'; // replace with actual enum or value
  showError: boolean = true;

  errorConfig = {
    required: this.isFi
      ? { msg: 'Please enter 9 alphanumeric characters.' }
      : { msg: 'Please enter from 3 to 5 characters.' },

    searchPattern: this.search.value !== ''
      ? this.isFi
        ? { msg: 'Please enter 9 alphanumeric characters.' }
        : { msg: 'Please enter valid input.' }
      : { msg: '' },

    securityResponse: this.search.value !== '' && this.search.value === 'invalid'
      ? { msg: 'Security not found.' }
      : { msg: '' },

    securitySearchError:
      this.search.value !== '' && this.search.value === 'somethingInvalid'
        ? this.tradeType.value === this.tradeTypeEnum
          ? { msg: 'Please enter valid trade type.' }
          : { msg: '' }
        : { msg: '' },
  };
}