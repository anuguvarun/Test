errorConfig(searchValue): string {
  const getMessage = (msgForCUSIP: string, msgForSymbol: string) =>
    isFI ? { msg: msgForCUSIP } : { msg: msgForSymbol };

  if (!searchValue?.required) {
    return getMessage('Please enter CUSIP.', 'Please enter symbol.').msg;
  }

  if (searchValue?.searchPattern?.value) {
    const length = searchValue.searchPattern.value.length;
    if (isFI && length !== 9) {
      return 'Please enter 9 alphanumeric characters.';
    }
    if (!isFI && (length < 2 || length > 5)) {
      return 'Please enter from 2 to 5 alphanumeric characters.';
    }
  }

  if (searchValue?.securityResponse?.value) {
    return 'Security not found.';
  }

  if (
    searchValue?.securitySearchError?.value &&
    !searchValue.searchOptions?.length
  ) {
    if (searchValue.tradeType?.value === tradeTypeEnum.MF) {
      return 'Please enter valid Mutual Fund symbol.';
    }
    if (searchValue.tradeType?.value === tradeTypeEnum.ETF) {
      return 'Please enter valid ETF symbol.';
    }
    return 'Security not found.';
  }

  if (searchValue?.duplicate) {
    return getMessage(
      'Duplicate entry, choose a different CUSIP.',
      'Duplicate entry, choose a different symbol.'
    ).msg;
  }

  return '';
}