lastEmittedValue: string = '';

onInput(value: string) {
  const trimmedValue = value.trim();

  if (trimmedValue && trimmedValue !== this.lastEmittedValue) {
    this.lastEmittedValue = trimmedValue;  // 🧠 Update last value
    this.searchService.call(trimmedValue).subscribe(...);  // 💥 Service call
  }
}

onBlur() {
  const value = this.search.value?.trim();
  if (!value) return;

  const triggerEmit = () => {
    if (value !== this.lastEmittedValue) {
      this.lastEmittedValue = value;  // 🧠 Update last value
      this.searchService.call(value).subscribe(...);  // 💥 Service call
    }
  };

  if (this.search.errors?.duplicate) {
    const interval = setInterval(() => {
      if (!this.search.errors?.duplicate) {
        clearInterval(interval);
        triggerEmit();  // only after error clears
      }
    }, 100);

    setTimeout(() => clearInterval(interval), 3000);
  } else {
    triggerEmit();  // no error, call immediately if needed
  }
}