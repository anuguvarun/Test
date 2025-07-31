lastEmittedValue: string = '';
lastEmitSource: 'input' | 'blur' | null = null;

onInput(value: string) {
  const trimmedValue = value.trim();
  if (trimmedValue && trimmedValue !== this.lastEmittedValue) {
    this.onSearchEmit(trimmedValue, 'input');
  }
}

onBlur() {
  const value = this.search.value?.trim();

  if (!value) return;

  if (this.search.errors?.duplicate) {
    // Wait for duplicate error to be cleared
    const interval = setInterval(() => {
      if (!this.search.errors?.duplicate) {
        clearInterval(interval);

        // Only call if input didn’t just call it
        if (this.lastEmittedValue !== value) {
          this.onSearchEmit(value, 'blur');
        }
      }
    }, 100);

    setTimeout(() => clearInterval(interval), 3000); // safety stop
  } else {
    // Also prevent double call here
    if (this.lastEmittedValue !== value) {
      this.onSearchEmit(value, 'blur');
    }
  }
}