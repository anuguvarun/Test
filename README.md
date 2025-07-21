errorConfig(searchValue): any {
  const getMsg = (msgForCUSIP: string, msgForSymbol: string) =>
    isFI ? { msg: msgForCUSIP } : { msg: msgForSymbol };

  return {
    required: !searchValue?.value
      ? getMsg('Please enter CUSIP.', 'Please enter symbol.')
      : undefined,

    searchPattern:
      searchValue?.value !== null && searchValue?.value !== undefined
        ? isFI
          ? searchValue.value.length !== 9
            ? { msg: 'Please enter 9 alphanumeric characters.' }
            : undefined
          : (searchValue.value.length < 2 || searchValue.value.length > 5)
            ? { msg: 'Please enter from 2 to 5 alphanumeric characters.' }
            : undefined
        : undefined,

    securityResponse:
      searchValue?.value !== null && searchValue?.value !== undefined
        ? { msg: 'Security not found.' }
        : undefined,

    securitySearchError:
      searchValue?.value !== null &&
      searchValue?.value !== undefined &&
      (!searchOptions || searchOptions.length === 0)
        ? searchValue?.tradeType?.value === tradeTypeEnum.MF
          ? { msg: 'Please enter valid Mutual Fund symbol.' }
          : searchValue?.tradeType?.value === tradeTypeEnum.ETF
            ? { msg: 'Please enter valid ETF symbol.' }
            : { msg: 'Security not found.' }
        : undefined,

    duplicate: isFI
      ? searchValue?.duplicate
        ? { msg: 'Duplicate entry, choose a different CUSIP.' }
        : undefined
      : searchValue?.duplicate
        ? { msg: 'Duplicate entry, choose a different symbol.' }
        : undefined
  };
}