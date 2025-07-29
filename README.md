get errorConfig() {
  const searchPatternMsg = (() => {
    if (this.search.value !== '' && this.search.value !== null) {
      if (this.isFi) {
        return { msg: 'Please enter 9 alphanumeric characters.' };
      } else {
        return { msg: 'Please enter from 2 to 5 alphanumeric characters.' };
      }
    }
    return { msg: '' };
  })();

  const securitySearchErrorMsg = (() => {
    if (
      this.search.value !== '' &&
      this.search.value !== null &&
      this.searchOptions?.length === 0
    ) {
      if (this.tradeType.value === this.tradeTypeEnum.MF) {
        return { msg: 'Please enter valid Mutual Fund symbol.' };
      } else if (this.tradeType.value === this.tradeTypeEnum.ETF) {
        return { msg: 'Please enter valid ETF symbol.' };
      } else {
        return { msg: 'Security not found.' };
      }
    }
    return { msg: '' };
  })();

  return {
    required: this.isFi
      ? { msg: 'Please enter CUSIP.' }
      : { msg: 'Please enter symbol.' },

    searchPattern: searchPatternMsg,

    securityResponse:
      this.search.value !== '' && this.search.value !== null
        ? { msg: 'Security not found.' }
        : { msg: '' },

    securitySearchError: securitySearchErrorMsg,

    duplicate: this.isFi
      ? { msg: 'Duplicate entry, choose a different CUSIP.' }
      : { msg: 'Duplicate entry, choose a different symbol.' },
  };
}