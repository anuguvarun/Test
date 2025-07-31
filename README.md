const formattedSearchValue = this.search.value?.toUpperCase();

let matched = false;
let matchedValue = formattedSearchValue;

if (this.searchOptions.length > 0) {
  const secData = this.searchOptions.find(
    (security) => security.value.toUpperCase() === formattedSearchValue
  );
  if (secData) {
    matched = true;
    matchedValue = secData.value;
  } else {
    this.search.setErrors({ securityResponse: true });
  }
}

// 🔥 Always emit the blur action, whether matched or not
this.onSearchSelectEmit({
  value: matchedValue,
  fromDropdown: matched
});