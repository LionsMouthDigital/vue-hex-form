<script>
  import axios from 'axios';

  export default {
    name: 'HexForm',

    props: {
      /**
       * Function to call after prepping the UI, after submitting the form.
       *
       * @type {Function}
       */
      afterSubmit: {
        type:    Function,
        default: () => () => {},
      },

      /**
       * Function to call after prepping the UI, before submitting the form.
       *
       * @type {Function}
       */
      beforeSubmit: {
        type:    Function,
        default: () => () => {},
      },

      /**
       * Selector for the submit button. Used to disable submissions while processing.
       *
       * @type {String}
       */
      buttonSelector: {
        type:    String,
        default: 'button',
      },

      /**
       * Message to display after failed submission.
       *
       * @type {String}
       */
      errorMessage: {
        type:    String,
        default: '<p>Looks like there was an error.</p>',
      },

      /**
       * Value in message component's ref attribute.
       *
       * @type {String}
       */
      messageRef: {
        type:     String,
        default: 'responseMessage',
      },

      /**
       * Message to display after a successful submission.
       *
       * @type {Object}
       */
      successMessage: {
        type:    String,
        default: '<p>Hurray! Your submission succeeded.</p>',
      },
    },


    data() {
      return {
        // Error data from a failed submission.
        errors:  {},
        // Message component as determined by this.messageRef.
        message: {},
      };
    },


    methods: {
      /**
       * Prep the UI after submitting.
       *
       * @author Curtis Blackwell
       * @return {void}
       */
      afterSubmitUiPrep() {
        // Enable button.
        this.$el.querySelector(this.buttonSelector).disabled = false;
        // Call hook.
        this.afterSubmit(this);
      },

      /**
       * Prep the UI before submitting.
       *
       * @author Curtis Blackwell
       * @return {void}
       */
      beforeSubmitUiPrep() {
        // Hide errors and message.
        this.clearErrors({});
        this.hideMessage();
        // Disable button.
        this.$el.querySelector(this.buttonSelector).disabled = true;
        // Call hook.
        this.beforeSubmit(this);
      },

      /**
       * Clear errors from the form.
       *
       * Pass an empty object to clear all errors.
       *
       * @author Curtis Blackwell
       * @param  {String} field Field name.
       * @return {void}
       */
      clearErrors(field) {
        // When no field is passed, Vue passes the event object. Use an empty object instead.
        this.errors = typeof field !== 'object' ? this.errors : {};
        this.errors.hasOwnProperty(field) ? this.$delete(this.errors, field) : '';
      },

      /**
       * Default function after a failed request.
       *
       * @author Curtis Blackwell
       * @param  {Object} error
       * @return {void}
       */
      failed(error) {
        this.showErrorMessage(error.response.data.message);
        this.errors = error.response.data.errors;
      },

      /**
       * Hide the message.
       *
       * @author Curtis Blackwell
       * @return {void}
       */
      hideMessage() {
        this.message.show = false;
      },

      /**
       * Make an AJAX request.
       *
       * @author Curtis Blackwell
       * @param  {String}   action    URL to hit. Falls back to form's action attribute.
       * @param  {Function} succeeded Function to call after successful form submission.
       *                              Accepts response object.
       *                              Falls back to this.succeeded().
       * @param  {Function} failed    Function to call after failed form submission.
       *                              Accepts error object.
       *                              Falls back to this.failed().
       * @return {void}
       */
      request(action, succeeded, failed) {
        axios
          .request({
            url:    action || this.$el.action,
            method: this.$el.method,
            data:   new FormData(this.$el),
          })
          .then((response) => {
            succeeded = succeeded || this.succeeded;
            succeeded(response);
            this.afterSubmitUiPrep();
          })
          .catch((error) => {
            failed = failed || this.failed;
            failed(error);
            this.afterSubmitUiPrep();
          });
      },

      /**
       * Show an error message.
       *
       * @author Curtis Blackwell
       * @param  {String} message If a response has a set message use it. Otherwise use the default.
       * @return {void}
       */
      showErrorMessage(message) {
        this.message.$el.classList.add('negative');
        this.message.$el.classList.remove('positive');

        this.message.html = message || this.errorMessage;
        this.message.show = true;
      },

      /**
       * Show a success message.
       *
       * @author Curtis Blackwell
       * @return {void}
       */
      showSuccessMessage() {
        this.message.$el.classList.add('positive');
        this.message.$el.classList.remove('negative');

        this.message.html = this.successMessage;
        this.message.show = true;
      },

      /**
       * Submit the form via AJAX.
       *
       * @author Curtis Blackwell
       * @param  {Object} $event Submission event. Passed automatically by Vue.
       * @return {void}
       */
      submitForm($event) {
        this.beforeSubmitUiPrep();
        this.request();
      },

      /**
       * Default function after a successful request.
       *
       * @author Curtis Blackwell
       * @param  {Object} response
       * @return {void}
       */
      succeeded(response) {
        if (response.data.success) {
          this.showSuccessMessage();
          // Handle redirection via Turbolinks.
          response.data.next ? setTimeout(location.assign, 1000, response.data.next) : '';
        }
      },
    },


    mounted() {
      // Set the message component.
      this.message = this.$parent.$refs[this.messageRef];
    },
  }
</script>
