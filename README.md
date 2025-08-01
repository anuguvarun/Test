import { Subscription } from 'rxjs';

export class YourComponent {
  @Input() search: FormGroup;

  private previousDuplicateError = false;
  private duplicateErrorSub?: Subscription;

  securityPriceSearchOnBlur(index: number): void {
    console.log('inside onBlur');

    if (this.tradeType.value === this.tradeTypeEnum.FI && this.search.value) {
      
      // If already subscribed, skip (or unsubscribe first if needed)
      if (this.duplicateErrorSub) {
        this.duplicateErrorSub.unsubscribe(); // Optional cleanup
      }

      this.duplicateErrorSub = this.search.statusChanges.subscribe(() => {
        const duplicateError = this.search.errors?.['duplicate'] ?? false;

        if (this.previousDuplicateError && !duplicateError) {
          this.onSearchEmit(); // Your function call
        }

        this.previousDuplicateError = duplicateError;
      });
    }
  }

  onSearchEmit() {
    console.log('Duplicate error cleared — emitting search');
    // Your logic here
  }

  ngOnDestroy(): void {
    // Cleanup to avoid memory leaks
    this.duplicateErrorSub?.unsubscribe();
  }
}