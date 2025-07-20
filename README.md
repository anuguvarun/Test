get errorConfig() {
  const searchVal = this.search?.value ?? '';
  const tradeTypeVal = this.tradeType?.value ?? '';
  const hasSearchVal = searchVal !== '';
  const hasSearchOptions = Array.isArray(this.searchOptions) && this.searchOptions.length > 0;

  console.log('[errorConfig] isFi:', this.isFi);
  console.log('[errorConfig] searchVal:', searchVal);
  console.log('[errorConfig] tradeTypeVal:', tradeTypeVal);
  console.log('[errorConfig] searchOptions:', this.searchOptions);
  console.log('[errorConfig] hasSearchVal:', hasSearchVal);
  console.log('[errorConfig] hasSearchOptions:', hasSearchOptions);

  const config = {
    required: this.isFi
      ? { msg: 'Please enter CUSIP.' }
      : { msg: 'Please enter symbol.' },

    searchPattern: hasSearchVal
      ? this.isFi
        ? { msg: 'Please enter 9 alphanumeric characters.' }
        : { msg: 'Please enter from 2 to 5 alphanumeric characters.' }
      : { msg: '' },

    securityResponse: hasSearchVal
      ? { msg: 'Security not found.' }
      : { msg: '' },

    securitySearchError: hasSearchVal && !hasSearchOptions
      ? tradeTypeVal === 'MF'
        ? { msg: 'Please enter valid Mutual Fund symbol.' }
        : tradeTypeVal === 'ETF'
        ? { msg: 'Please enter valid ETF symbol.' }
        : { msg: 'Security not found.' }
      : { msg: '' },

    duplicate: this.isFi
      ? { msg: 'Duplicate entry, choose a different CUSIP.' }
      : { msg: 'Duplicate entry, choose a different symbol.' },
  };

  console.log('[errorConfig] generated config:', config);
  return config;
}