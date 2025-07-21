get errorConfig() {
  const searchVal = this.search?.value ?? '';
  const tradeTypeVal = this.tradeType?.value ?? '';
  const hasSearchVal = searchVal !== '';
  const hasSearchOptions = Array.isArray(this.searchOptions) && this.searchOptions.length > 0;

  console.log('[errorConfig] this.search:', this.search);
  console.log('[errorConfig] this.search.value:', this.search?.value);
  console.log('[errorConfig] hasSearchVal:', hasSearchVal);
  console.log('[errorConfig] hasSearchOptions:', hasSearchOptions);

  const config: any = {};

  if (this.isFi) {
    config.required = { msg: 'Please enter CUSIP.' };
  } else {
    config.required = { msg: 'Please enter symbol.' };
  }

  if (hasSearchVal) {
    config.searchPattern = this.isFi
      ? { msg: 'Please enter 9 alphanumeric characters.' }
      : { msg: 'Please enter from 2 to 5 alphanumeric characters.' };
  } else {
    config.searchPattern = { msg: '' };
  }

  config.securityResponse = hasSearchVal ? { msg: 'Security not found.' } : { msg: '' };

  if (hasSearchVal && !hasSearchOptions) {
    if (tradeTypeVal === 'MF') {
      config.securitySearchError = { msg: 'Please enter valid Mutual Fund symbol.' };
    } else if (tradeTypeVal === 'ETF') {
      config.securitySearchError = { msg: 'Please enter valid ETF symbol.' };
    } else {
      config.securitySearchError = { msg: 'Security not found.' };
    }
  } else {
    config.securitySearchError = { msg: '' };
  }

  config.duplicate = this.isFi
    ? { msg: 'Duplicate entry, choose a different CUSIP.' }
    : { msg: 'Duplicate entry, choose a different symbol.' };

  return config;
}