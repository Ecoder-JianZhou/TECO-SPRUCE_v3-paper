real(8) :: FRLEN(nlayers)        ! ratio of roots in every layer, Oak Ridge FACE: Shuang
to species-based parameters site-based recalculated by different species

st%LAImax st%LAImin 
st%LAI must update 
summary by weighted summary

real(8) :: etaW           !!!! need to be assigned value

Esoil calculate in vegetation module. 
Esoil re-calculated in Soil Tem module, the only different is that it adds the water_table_depth to limit the Rsoil
But the Tsoil_simu is called in vegetation module.
Esoil must be calcualted in Tsoil_simu with the average site-based LAI.

in soil T :! drsdh    = 0.0    ! temporarily set drsdh =0 for heat adjustment of soil when  

after xlaryer, summary the QLair, QLleaf, Rsoilab1, Rsoilab2 

remind: bmleaf bmstem bmroot

NPP drop down?
GrowthP related
NSN related
st%sp(ipft)%N_transfer + st%sp(ipft)%N_uptake + st%sp(ipft)%N_fixation
st%sp(ipft)%N_leaf+st%sp(ipft)%N_Stem+st%sp(ipft)%N_root\

N_transfer = 0; 

QNminer < 0; N_loss too large; runoff suddenly increased
before it, shrub and Sphagnum GPP and NPP to be zero

fNnetmin = 0
N_demand = 0

need to add "st%add" to "NPPother"

How to add parameters:
parameters.nml add the parameters in the back
st and spec parameters name
in_site_params and in_spec_params: add the parameter name
io_mod: read_nml_params_initValues, 
    add the parameter names to read the values
    add to the relevent namelist
    add to the in_params_vals value

initialize_teco:
    init_spec_params:
    update_spec_params: call paremters and values

mcmc_configs.nml: add the range of parameters

readMCMC_configs_NML: add the parameters, namelist, nspec_params, call update

mc_update_params4simu: add the new parameters

2023-11-07: 
    1. use the 2014 shrub, sphagnum, Tree 

    How to add new observation to do MCMC:
        1. type allCostVariables, add the variable 
        2. add the obsfile___ variable in mcmc_mod.f90
        3. add the obsfile___ variable name in subroutine readMCMC_configs_NML namelist/nml_obsfiles/
        4. add the obsfile___ in the mcmc_configs.nml
        5. give the value to the vars4MCMC%LAI_d%filepath in readMCMC_configs_NML
        6. initialize vars4MCMC%cPlant_tree_y%mc_itime     = 1 in "mcmc_functions_init"
        7. call readObsData_var(vars4MCMC%cPlant_tree_y) in subroutine readObsData()
        8. call GetSimuData_var(vars4MCMC%cPlant_tree_y, outVars_d%sp(1)%cLeaf+outVars_d%sp(1)%cRoot+outVars_d%sp(1)%cRoot)
        9. if(vars4MCMC%cPlant_tree_y%existOrNot)then
                call CalculateCost(vars4MCMC%cPlant_tree_y%mdData(:,4), vars4MCMC%cPlant_tree_y%obsData(:,4),&
                    vars4MCMC%cPlant_tree_y%obsData(:,5), J_cost)
                J_new(2) = J_new(2) + J_cost
            endif
--------------------------------------------------------------------------------------------------
# put the pool size to do data assimilation
    ! add for pool sizes
    f_fast       = 0.01
    f_slow       = 0.55
    s_soil       = 1.
    ! in different species
    ! add for pool sizes
    s_cLeaf  = 1.0,    1.0,    1.0
    s_cStem  = 1.0,    1.0,    1.0
    s_sRoot  = 1.0,    1.0,    1.0 
    s_nsc    = 1.0,    1.0,    1.0

    2. add parameters in type in_site_params or type in_spec_params
    3. add the parameters in subroutine read_nml_params_initValues in io_mod to read the parameters
        3-1. parameter variables
        3-2. namelist 
        3-3. give the values
    5. add the parameters to site-based and species-based parameters
    
    4. add the parameters to initialize_teco:
        4-1. subroutine update_site_params  add the parameters
        4-2. update the "call update_site_params() in subroutine init_site_params"
        4-3. subroutine update_spec_params
        4-4. update the "call update_spec_params() in subroutine init_spec_params"
        4-5. add the intial value changes in subroutine initialize_teco(st)
    
2015 anpp shrub and tree is error

# add new observation data:
    mcmc_mod: add variable of "obsfile_cPlant_sphag_y"
    mcmc_configs: add variable of "obsfile_cPlant_sphag_y"

datatypes.f90
    type spec_outvars_type: add "Aleaf(3)"
    subroutine init_spec_outVars(spec_outVars): add spec_outVars%Aleaf(3)    = 0.
    type spec_data_type: add "Aleaf(3)"