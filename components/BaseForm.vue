<script>

function computeClass(defaultClass, additionalClass) {
    if (!defaultClass) return ''
    return additionalClass ? `${defaultClass} ${additionalClass}` : defaultClass
}

export const BaseForm = {
    name: 'BaseForm',
    props: {
        url: String,
        customSubmit: Boolean
    },
    render() {
        return <form url={this.url} class={computeClass} onSubmit={this.handleSubmit}>
            {this.$slots.default}
        </form>
    },
    methods: {
        handleSubmit(e) {
            e.preventDefault()
            e.stopPropagation()

            if (this.customSubmit) {
                this.$emit('submit', e)
            } else {
                const data = new FormData(e.target)
                fetch(this.url, {
                    method: 'POST',
                    // headers: this.$root.defaultHeaders,
                    body: data
                })
                    .then(response => response.json())
                    .then(response => {
                        console.log(response)
                        this.showSuccess()
                        this.clearForm(e.target)
                    })
                    .catch(e => {
                        this.showError(e)
                        console.log(e)
                    })
            }
        },
        clearForm(form) {
            form.reset()
        },
        showSuccess() {},
        showError() {}
    }
}

export const BaseInput = {
    name: 'BaseInput',
    props: {
        type: {
            type: String,
            default: 'text'
        },
        name: String,
        value: {
            type: String,
            default: ''
        },
        placeholder: {
            type: String,
            default: ''
        },
        wrapperClass: String,
        elClass: String
    },
    render() {
        return (
            <div class={computeClass('form-row', this.wrapperClass)}>
                <input
                    type={this.type}
                    name={this.name}
                    v-model={this.model}
                    value={this.value}
                    class={computeClass('pcore-input', this.elClass)}
                    placeholder={this.placeholder}
                    aria-label={this.placeholder}
                />
            </div>
        )
    },
    data() {
        return {
            model: null
        }
    }
}

export const BaseButton = {
    props: {
        type: {
            type: String,
            default: 'button'
        },
        text: {
            type: String,
            default: 'Отправить'
        },
        icon: String,
        eventAction: String,
        stopPrevent: {
            type: Boolean,
            default: false
        }
    },
    render() {
        const hasIcon = () => this.icon ? <i class={this.icon}></i> : false

        return (
            <div class={computeClass('form-row', this.wrapperClass)}>
                <button
                    type={this.type}
                    class={computeClass('pcore-btn', this.elClass)}
                    onClick={
                        (e) => {
                            console.log(e.target)
                            e.stopPropagation()
                            // if (!this.stopPrevent) {
                            //     e.preventDefault()
                            // }

                            if (this.type === 'submit') {
                                this.$emit('submit', e)
                            }

                            if (this.eventAction) this.$emit(this.eventAction, e)
                        }
                    }
                >
                    {hasIcon()}
                    <span>{this.text}</span>
                </button>
            </div>
        )
    }
}

export default BaseForm

</script>
