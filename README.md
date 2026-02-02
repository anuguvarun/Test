List<String> unApprovedRequestStatuses =
        List.of(
            TradeOrderRequestStatusEnum.SUBMITTED.name(),
            TradeOrderRequestStatusEnum.IN_REVIEW.name(),
            TradeOrderRequestStatusEnum.PROCESSING.name()
        );

List<String> unApprovedItemStatuses =
        List.of(
            TradeOrderItemStatusEnum.SUBMITTED.getStatusCode(),
            TradeOrderItemStatusEnum.FAILED.getStatusCode()
        );

Long unApprovedCount =
        repository.getUnApprovedCount(
            unApprovedRequestStatuses,
            unApprovedItemStatuses
        );