public TradeOrderSummaryResponse getSummary() {

        List<String> unApprovedItemStatuses =
                List.of("PENDING_APPROVAL", "AWAITING_REVIEW");

        List<String> unsettledItemStatuses =
                List.of("UNSETTLED", "PARTIALLY_SETTLED");

        Long unApprovedCount =
                repository.getUnApprovedCount(unApprovedItemStatuses);

        Long unsettledCount =
                repository.getUnsettledCount(unsettledItemStatuses);

        TradeOrderSummaryResponse response = new TradeOrderSummaryResponse();
        response.setUnApprovedCount(unApprovedCount.intValue());
        response.setUnsettledCount(unsettledCount.intValue());
        response.setCurrentSystemDateTime(ZonedDateTime.now());

        return response;
    }