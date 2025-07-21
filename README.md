get errorConfig() {
  const searchVal = this.search?.value ?? '';
  const tradeTypeVal = this.tradeType?.value ?? '';
  const hasSearchVal = searchVal !== '';
  const noSearchOptions = this.searchOptions?.length === 0;

  // 1. Required field
  if (!hasSearchVal) {
    return {
      required: this.isFi
        ? { msg: 'Please enter CUSIP.' }
        : { msg: 'Please enter symbol.' },
    };
  }

  // 2. Search pattern validation
  if (this.isFi && searchVal.length !== 9) {
    return { searchPattern: { msg: 'Please enter 9 alphanumeric characters.' } };
  }
  if (!this.isFi && (searchVal.length < 2 || searchVal.length > 5)) {
    return { searchPattern: { msg: 'Please enter from 2 to 5 alphanumeric characters.' } };
  }

  // 3. Security search error
  if (noSearchOptions) {
    if (tradeTypeVal === 'MF') {
      return { securitySearchError: { msg: 'Please enter valid Mutual Fund symbol.' } };
    } else if (tradeTypeVal === 'ETF') {
      return { securitySearchError: { msg: 'Please enter valid ETF symbol.' } };
    } else {
      return { securitySearchError: { msg: 'Security not found.' } };
    }
  }

  // 4. Security not found fallback
  if (hasSearchVal) {
    return { securityResponse: { msg: 'Security not found.' } };
  }

  // 5. Duplicate
  return {
    duplicate: this.isFi
      ? { msg: 'Duplicate entry, choose a different CUSIP.' }
      : { msg: 'Duplicate entry, choose a different symbol.' },
  };
}