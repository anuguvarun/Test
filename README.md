get errorConfig() {
  const config: any = {};

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

  // Required
  if (this.isFi) {
    config.required = { msg: 'Please enter CUSIP.' };
  } else {
    config.required = { msg: 'Please enter symbol.' };
  }

  // Search Pattern
  if (hasSearchVal) {
    if (this.isFi) {
      config.searchPattern = { msg: 'Please enter 9 alphanumeric characters.' };
    } else {
      config.searchPattern = { msg: 'Please enter from 2 to 5 alphanumeric characters.' };
    }
  } else {
    config.searchPattern = { msg: '' };
  }

  // Security Response
  if (hasSearchVal) {
    config.securityResponse = { msg: 'Security not found.' };
  } else {
    config.securityResponse = { msg: '' };
  }

  // Security Search Error
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

  // Duplicate
  if (this.isFi) {
    config.duplicate = { msg: 'Duplicate entry, choose a different CUSIP.' };
  } else {
    config.duplicate = { msg: 'Duplicate entry, choose a different symbol.' };
  }

  console.log('[errorConfig] final config:', config);
  return config;
}