import { Component } from '@angular/core';

@Component({
  selector: 'your-component',
  templateUrl: './your-component.component.html'
})
export class YourComponent {
  isFi = true; // example
  search = { value: '', pristine: true }; // example structure
  tradeType = { value: '' }; // example
  tradeTypeEnum = { MF: 'MF', ETF: 'ETF' };
  searchOptions: any[] = [];

  errorConfig = this.getErrorConfig();

  getErrorConfig() {
    return {
      required: this.isFi
        ? { msg: 'Please enter CUSIP.' }
        : { msg: 'Please enter symbol.' },

      searchPattern:
        this.search.value !== '' && this.search.value !== null
          ? this.isFi
            ? { msg: 'Please enter 9 alphanumeric characters.' }
            : { msg: 'Please enter from 2 to 5 alphanumeric characters.' }
          : null,

      securityResponse:
        this.search.value !== '' && this.search.value !== null
          ? { msg: 'Security not found.' }
          : null,

      securitySearchError:
        this.search.value !== '' &&
        this.search.value !== null &&
        this.searchOptions?.length === 0
          ? this.tradeType.value === this.tradeTypeEnum.MF
            ? { msg: 'Please enter valid Mutual Fund symbol.' }
            : this.tradeType.value === this.tradeTypeEnum.ETF
            ? { msg: 'Please enter valid ETF symbol.' }
            : { msg: 'Security not found.' }
          : null,

      duplicate: this.isFi
        ? { msg: 'Duplicate entry, choose a different CUSIP.' }
        : { msg: 'Duplicate entry, choose a different symbol.' },
    };
  }

  showErrorsIf(clean: boolean): boolean {
    return this.search.pristine && clean;
  }
}