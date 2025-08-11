getError() {
  const val = (this.search?.value ?? '').trim();
  const alnum = /^[a-z0-9]+$/i;

  // 1) required
  if (!val) {
    return {
      key: 'required',
      msg: this.isFi ? 'Please enter CUSIP.' : 'Please enter symbol.'
    };
  }

  // 2) search pattern (always before duplicate)
  if (this.isFi) {
    const ok = alnum.test(val) && val.length === 9;
    if (!ok) {
      return {
        key: 'searchPattern',
        msg: 'Please enter 9 alphanumeric characters.'
      };
    }
  } else {
    const ok = alnum.test(val) && val.length >= 2 && val.length <= 5;
    if (!ok) {
      return {
        key: 'searchPattern',
        msg: 'Please enter from 2 to 5 alphanumeric characters.'
      };
    }
  }

  // 3) security search result errors
  const noOptions =
    !Array.isArray(this.searchOptions) || this.searchOptions.length === 0;

  if (noOptions) {
    if (this.tradeType?.value === this.tradeTypeEnum.MF) {
      return { key: 'security', msg: 'Please enter valid Mutual Fund symbol.' };
    }
    if (this.tradeType?.value === this.tradeTypeEnum.ETF) {
      return { key: 'security', msg: 'Please enter valid ETF symbol.' };
    }
    return { key: 'security', msg: 'Security not found.' };
  }

  // 4) duplicate (only if all the above passed)
  const isDup =
    typeof this.isDuplicate === 'function'
      ? this.isDuplicate(val)
      : !!this.duplicate; // adapt to your existing flag

  if (isDup) {
    return {
      key: 'duplicate',
      msg: this.isFi
        ? 'Duplicate entry, choose a different CUSIP.'
        : 'Duplicate entry, choose a different symbol.'
    };
  }

  // ✅ no error
  return null;
}