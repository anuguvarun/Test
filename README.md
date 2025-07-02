<fcdl-segmented-control
  [control]="actionToggle"
  [errorConfig]="{
    required: { msg: 'Please select trade request type.' },
    securityHeldValidator: { msg: 'Security not held in this account.' }
  }"
  [showErrorsIfClean]="
    (actionToggle.errors?.securityHeldValidator && (actionToggle.dirty || showError)) ||
    (actionToggle.errors?.required && (actionToggle.touched || showError))
  "
  ...
></fcdl-segmented-control>