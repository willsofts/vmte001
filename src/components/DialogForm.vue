<template>
<div :id="id" class="modal fade pt-page pt-page-item" tabindex="-1" role="dialog" v-bind="$attrs">
    <div class="modal-dialog" :class="sizing">
        <div class="modal-content portal-area fa-portal-area">
            <div class="modal-header">
                <slot name="header"></slot>
                <button type="button" class="close btn-closing" data-dismiss="modal"></button>
            </div>
            <div class="entry-dialog-layer">
                <slot></slot>
            </div>
            <div class="row-heighter modal-footer" >
                <slot name="footer"></slot>
			</div>
		</div>
	</div>
</div>
</template>
<script>
export default {
    props: {
        id: {
            type: String,
            default: "modaldialog_layer"
        },
        sizing: {
            type: String,
            default: "modal-lg"
        }
    }
};
</script>        
