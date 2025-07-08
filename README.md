@ViewChild('actionSegment', { static: false }) actionSegment: ElementRef;

handleSearchKeyDown(event: KeyboardEvent): void {
  if (event.key === 'Tab') {
    const secData = this.searchOptions.find(
      (security) => security.value === this.search.value
    );

    if (secData) {
      event.preventDefault(); // Stop Tab from moving focus

      this.onSearchSelectEmit();

      setTimeout(() => {
        const el: HTMLElement | null = this.actionSegment?.nativeElement;
        if (el) {
          const focusable = el.querySelector('input, button, [tabindex]:not([tabindex="-1"])') as HTMLElement;
          focusable?.focus();
        }
      });
    }
  }
}