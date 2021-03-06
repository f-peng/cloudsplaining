<template>
    <div v-if="findings(policyId, 'PrivilegeEscalation').length > 0">
        <div class="card-header">
            <a class="card-link" data-toggle="collapse"
               v-bind:data-parent="'#' + inlineOrManaged.toLowerCase() + '-policy' + '-' + policyId + '-' + 'card-details'"
               v-bind:href="'#' + inlineOrManaged.toLowerCase() + '-policy' + '-' + policyId + '-' +'privilege-escalation'"
            >Privilege Escalation</a>
        </div>
        <div class="panel-collapse collapse"
             ref="PrivilegeEscalationDetailsDiv"
             v-bind:id="inlineOrManaged.toLowerCase() + '-policy' + '-' + policyId + '-' +'privilege-escalation'">
            <!--TODO: Format the Privilege Escalation stuff-->
            <div class="card-body">
                <!--What should I do?-->
                <b-button squared variant="link" v-b-modal="`${inlineOrManaged.toLowerCase()}-policy-${policyId}-privilege-escalation-what-should-i-do`">What should I do?</b-button>
                <b-modal v-bind:id="`${inlineOrManaged.toLowerCase()}-policy-${policyId}-privilege-escalation-what-should-i-do`">
                    <p>
                        <span v-html="whatShouldIDoDescription"></span>
                    </p>
                </b-modal>
                <!--Triaging: Identifying False Positives-->
                <b-button squared variant="link" v-b-modal="`${inlineOrManaged.toLowerCase()}-policy-${policyId}-privilege-escalation-identifying-false-positives`">How do I identify False Positives?</b-button>
                <b-modal v-bind:id="`${inlineOrManaged.toLowerCase()}-policy-${policyId}-privilege-escalation-identifying-false-positives`">
                    <p>
                        <span v-html="identifyingFalsePositivesDescription"></span>
                    </p>
                </b-modal>
                <!--How do I validate results?-->
                <b-button squared variant="link" v-b-modal="`${inlineOrManaged.toLowerCase()}-policy-${policyId}-privilege-escalation-validating-fixed-policy`">How do I fix it?</b-button>
                <b-modal v-bind:id="`${inlineOrManaged.toLowerCase()}-policy-${policyId}-privilege-escalation-validating-fixed-policy`">
                    <p>
                        <span v-html="howDoIValidateResultsDescription"></span>
                    </p>
                </b-modal>
                <br>
                <br>
                <span v-html="getRiskDescription('PrivilegeEscalation')"></span>
                <span>Action combinations:</span>
<pre><code>
{{ JSON.parse(JSON.stringify(findings(policyId, 'PrivilegeEscalation'), undefined, '\t')) }}
</code></pre>
            </div>
        </div>
    </div>
</template>

<script>
    import whatShouldIDoRaw from "../../assets/what-should-i-do.md";

    const managedPoliciesUtil = require('../../util/managed-policies');
    const inlinePoliciesUtil = require('../../util/inline-policies');
    var md = require('markdown-it')({
        html: true,
        linkify: true,
        typographer: true
    });
    import privilegeEscalationRaw from '../../assets/definition-privilege-escalation.md'
    import identifyingFalsePositivesDescriptionRaw from "../../assets/identifying-false-positives.md";
    import howDoIValidateResultsRaw from "../../assets/how-do-i-validate-results.md";
    const privilegeEscalationDescription = md.render(privilegeEscalationRaw)

    const whatShouldIDoDescription = md.render(whatShouldIDoRaw)
    const identifyingFalsePositivesDescription = md.render(identifyingFalsePositivesDescriptionRaw)
    const howDoIValidateResultsDescription = md.render(howDoIValidateResultsRaw)


    export default {
        name: "PrivilegeEscalationDetails",
        inject: ['toggleData'],
        props: {
            // Either "Inline", "AWS", or "Customer"
            managedBy: {
                type: String
            },
            policyId: {
                type: String
            },
            iam_data: {
                type: Object
            },
        },
        computed: {
            inlineOrManaged() {
                if ((this.managedBy === "AWS") || (this.managedBy === "Customer")) {
                    return "Managed"
                } else {
                    return "Inline"
                }
            },
            whatShouldIDoDescription() {
                return whatShouldIDoDescription
            },
            identifyingFalsePositivesDescription() {
                return identifyingFalsePositivesDescription
            },
            howDoIValidateResultsDescription() {
                return howDoIValidateResultsDescription
            },
        },
        methods: {
            findings: function (policyId, riskType) {
                if (this.managedBy === "Inline") {
                    return inlinePoliciesUtil.getInlinePolicyFindings(this.iam_data, policyId, riskType)
                } else {
                    return managedPoliciesUtil.getManagedPolicyFindings(this.iam_data, this.managedBy, policyId, riskType);
                }
            },
            getRiskDescription: function (riskType) {
                if (riskType === "PrivilegeEscalation") {
                    return privilegeEscalationDescription
                }
            }
        },
        watch: {
            toggleData: {
                handler(data) {
                    if (data.isAllExpanded && this.$refs['PrivilegeEscalationDetailsDiv']) {
                        this.$refs['PrivilegeEscalationDetailsDiv'].classList.add('show');
                    }
                    if (data.isAllCollapsed && this.$refs['PrivilegeEscalationDetailsDiv']) {
                        this.$refs['PrivilegeEscalationDetailsDiv'].classList.remove('show');
                    }
                },
                deep: true
            }
        }
    }
</script>

<style scoped>

</style>
