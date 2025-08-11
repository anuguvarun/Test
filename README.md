getErrorConfig() {
  const val = this.search && this.search.value != null ? this.search.value : '';

  // required
  const required = {};
  if (!val) {
    required.msg = this.isFi ? 'Please enter CUSIP.' : 'Please enter symbol.';
  } else {
    required.msg = '';
  }

  // search pattern (original logic)
  const searchPattern = {};
  if (val !== '' && val != null) {
    if (this.isFi) {
      searchPattern.msg = 'Please enter 9 alphanumeric characters.';
    } else {
      searchPattern.msg = 'Please enter from 2 to 5 alphanumeric characters.';
    }
  } else {
    searchPattern.msg = '';
  }

  // security search errors (original logic, no ternary, no Array.isArray)
  const securitySearchErrors = {};
  if (
    val !== '' &&
    val != null &&
    this.searchOptions?.length === 0
  ) {
    if (this.tradeType && this.tradeType.value === this.tradeTypeEnum.MF) {
      securitySearchErrors.msg = 'Please enter valid Mutual Fund symbol.';
    } else if (this.tradeType && this.tradeType.value === this.tradeTypeEnum.ETF) {
      securitySearchErrors.msg = 'Please enter valid ETF symbol.';
    } else {
      securitySearchErrors.msg = 'Security not found.';
    }
  } else {
    securitySearchErrors.msg = '';
  }

  // securityResponse (unchanged)
  const securityResponse = {};
  if (val !== '' && val != null) {
    securityResponse.msg = 'Security not found.';
  } else {
    securityResponse.msg = '';
  }

  // duplicate (only if no search pattern or security search errors)
  const duplicate = {};
  if (
    !searchPattern.msg &&
    !securitySearchErrors.msg
  ) {
    if (this.isFi) {
      duplicate.msg = 'Duplicate entry, choose a different CUSIP.';
    } else {
      duplicate.msg = 'Duplicate entry, choose a different symbol.';
    }
  } else {
    duplicate.msg = '';
  }

  return {
    required,
    searchPattern,
    securityResponse,
    securitySearchErrors,
    duplicate
  };
}