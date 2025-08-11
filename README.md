getError() {
  const val = this.search?.value;

  // 1) required
  if (!val) {
    return {
      key: 'required',
      msg: this.isFi ? 'Please enter CUSIP.' : 'Please enter symbol.'
    };
  }

  // 2) search pattern message (your existing logic)
  if (val !== '' && val != null) {
    if (this.isFi) {
      return { key: 'searchPattern', msg: 'Please enter 9 alphanumeric characters.' };
    } else {
      return { key: 'searchPattern', msg: 'Please enter from 2 to 5 alphanumeric characters.' };
    }
  }

  // 3) security search errors (your existing logic)
  if (
    val !== '' &&
    val != null &&
    this.searchOptions?.length === 0
  ) {
    if (this.tradeType?.value === this.tradeTypeEnum.MF) {
      return { key: 'security', msg: 'Please enter valid Mutual Fund symbol.' };
    } else if (this.tradeType?.value === this.tradeTypeEnum.ETF) {
      return { key: 'security', msg: 'Please enter valid ETF symbol.' };
    } else {
      return { key: 'security', msg: 'Security not found.' };
    }
  }

  // 4) duplicate (last priority)
  return this.isFi
    ? { key: 'duplicate', msg: 'Duplicate entry, choose a different CUSIP.' }
    : { key: 'duplicate', msg: 'Duplicate entry, choose a different symbol.' };
}